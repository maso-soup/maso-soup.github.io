
The find action will start in the current directory, searching by name for files that match the ".page" search string. Directories will not be listed because we're specifically telling it to look for files only, with `-type f`. The `print0`argument tells `find` to not treat whitespace as the end of a filename. This means that that filenames with spaces in them will be processed correctly.

Find file name, with wild card:

`find ./ -name "*.txt" -type f`

Find text in files:

`find / "*text*" -type f 2>/dev/null`

To hide errors while searching, the following can be used to direct standard error to /dev/null:

`find ./ -name "*.txt" -type f -print0 2>/dev/null



`find . -type f -size 1033c ! -executable -exec file {} + | grep ASCII``

#find #cli-tool 
