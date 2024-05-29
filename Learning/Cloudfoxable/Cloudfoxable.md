# Do this first!

## First Flag

### Overview

- **Author:** [Seth Art](https://www.linkedin.com/in/sethart/) ([@sethsec](https://x.com/sethsec))
- **Default state:** Enabled
- **Estimated Cost:** No cost
- **Starting Point:** [https://github.com/BishopFox/cloudfoxable](https://github.com/BishopFox/cloudfoxable)

**Welcome!**

CloudFoxable is Terraform code that lives at [https://github.com/BishopFox/cloudfoxable](https://github.com/BishopFox/cloudfoxable). In order to hack against CloudFoxable, you'll have to create a new playground AWS account or use an existing one. You'll then need to install Terraform and deploy CloudFoxable via Terraform. Once you do that, you can use this CTFd instance to walk you through the challenges, and optionally, you can register and submit your flags here.

**Setup Instructions**

1. Select or create an AWS account. (Do NOT use an account that has any production resources or sensitive data!)
2. [Create a non-root user with administrative access](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) that you will use when running Terraform.
3. [Create an access key for that user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html).
4. [Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html).
5. [Configure your AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) with your newly created admin user as the default profile.
6. Confirm your CLI is working as expected by executing `aws sts get-caller-identity`.
7. [Install the Terraform binary](https://www.terraform.io/downloads.html) and add the binary location to your path.
8. `git clone https://github.com/BishopFox/cloudfoxable`
9. `cd cloudfoxable/aws`
10. `cp terraform.tfvars.example terraform.tfvars`
11. `terraform init`
12. Edit `terraform.tfvars` and set the AWS profile you'd like to use: `aws_local_profile = "YOUR_PROFILE"`
13. (Optional) `terraform plan`
14. `terraform apply`
15. Install CloudFox, Pmapper, Pacu and any other AWS Cloud Penetration Testing tools you like to use.

**Find the Flag**

Read the stuff at the end of the `terraform apply` output closely enough, and you'll find the first flag. The Terraform output will show you how to set up your AWS CLI with the `ctf-starting-user`.

By the way, the flag syntax is always:

`FLAG{challengeName::CamelCaseText}`

You will always submit this whole string as shown above with the word flag.

**Clean up**

At any point, if you want to remove all CloudFoxable challenges, you can run `terraform destroy`from the `cloudfoxable/aws` directory.

### Notes

![[Pasted image 20240526182302.png]]

```
echo "" >> ~/.aws/credentials && echo "[cloudfoxable]" >> ~/.aws/credentials && echo "aws_access_key_id = `../../terraform output -raw CTF_Start_User_Access_Key_Id`" >> ~/.aws/credentials && echo "aws_secret_access_key = `../../terraform output -raw CTF_Start_User_Secret_Access_Key`" >> ~/.aws/credentials && echo "region = us-west-2" >> ~/.aws/credentials
```

# Assumed Breach: Application Compromise/Network Access

## Bastion

## Variable

## Wyatt

# Assumed Breach: Principal

## It's a Secret

### Overview

- **Author:** [Seth Art](https://www.linkedin.com/in/sethart/) ([@sethsec](https://x.com/sethsec))
- **Default state:** Enabled
- **Estimated Cost:** $0.10/month
- **Starting Point:** `arn:aws:iam::ACCOUNT_ID:user/ctf-starting-user`

**CloudFoxable Setup**

None required.

**Challenge Details**

For this CTF, your starting CTF user has the following policies:

- SecurityAudit (AWS Managed)
- CloudFox (Customer Managed)
- its-a-secret (Customer Managed)

The first two policies allow you to run CloudFox. The third policy allows this starting user to get the flag for this challenge. If you followed the setup steps in the Terraform output, you'll have a profile called `cloudfoxable` which is tied to the `user/ctf-starting-user`.

To confirm this, run `aws --profile cloudfoxable sts get-caller-identity`.

Now run `cloudfox` using the `cloudfoxable` profile and see if you can access the secret named `its-a-secret`.

**Cleanup Tasks**

None required.

### Notes

Ran `cloudfox` with `all-checks` to see what resulted:

```
cloudfox aws -p cloudfoxable all-checks -v2
```

Reviewed the `loot` output and noted the commands in `pull-secrets-commands.txt`

Ran the command in the file below to reveal the flag:

```
aws --profile cloudfoxable --region us-west-2 ssm get-parameter --with-decryption --name /cloudfoxable/flag/its-a-secret
```

## It's Another Secret

### Overview

- **Author:** [Seth Art](https://www.linkedin.com/in/sethart/) ([@sethsec](https://x.com/sethsec))
- **Default state:** Enabled
- **Estimated Cost:** $0.05/month
- **Starting Point:** `arn:aws:iam::ACCOUNT_ID:role/Ertz`

**CloudFoxable Setup**

None required

**Challenge Details**

TL;DR: You've just gained access to the role `Ertz`. Can you find and access the `its-another-secret`flag?

A lot of the challenges in the category **Assumed Breach: Principal** will have you assume into a role to simulate a new starting point. You'll technically start as `ctf-starting-user`, but your first action will be to assume the role `Ertz` listed above. This is to simulate a scenario where you've just gained access to the role `Ertz`.

You can do this the "hard way" by running `aws --profile cloudfoxable sts assume-role --role-arn arn:aws:iam::533267344535:role/Ertz --role-session-name Ertz` and then using that output to set up a new profile or environment variables.

Or, you can do it the easy way and create a new profile that does the role assumption for you!

Edit `~/.aws/config` and add the following profile:

```
[profile ertz]
region = us-west-2
role_arn = arn:aws:iam::533267344535:role/Ertz
source_profile = cloudfoxable
```

Now verify it!

```
❯ aws --profile ertz sts get-caller-identity
{
    "UserId": "AROAQXHJKLZKFYSRACOES:botocore-session-1684201365",
    "Account": "533267344535",
    "Arn": "arn:aws:sts::ACCOUNT_ID:assumed-role/Ertz/botocore-session-1684201365"
}
```

For each of these challenges, and on a cloud penetration test, the next thing you'll want to do is see what permissions the assumed breach user has, and see if any of them are "interesting".

If you've completed `It's a secret`, the rest of this challenge will look the same. The main goal here is to make sure you are comfortable assuming new roles. Good luck!

**Cleanup Tasks**

None required

Followed above instructions.

Ran `cloudfox` with `all-checks` to see what resulted:

```
cloudfox aws -p Ertz all-checks -v2
```

Reviewed the `loot` output and noted the commands in `pull-secrets-commands.txt`

Ran the command in the file below to reveal the flag:

```
aws --profile Ertz --region us-west-2 ssm get-parameter --with-decryption --name /cloudfoxable/flag/its-a-secret
```


## Backwards

### Overview

- **Author:** [Seth Art](https://www.linkedin.com/in/sethart/) ([@sethsec](https://x.com/sethsec))
- **Default state:** Enabled
- **Estimated Cost:** $0.80/month
- **Starting Point:** `arn:aws:secretsmanager:us-west-2:ACCOUNTID:secret:DomainAdministrator-Credentials-SUFFIX`

**CloudFoxable Setup**

None required

**Challenge Details**

In some challenges, you might not see an IAM role or an IP address as the starting point, but rather, an interesting ARN or something like that.

Sometimes during cloud penetration tests, we first find something interesting and then work backwards to see who has access to it. Is it just the Administrators? Well, that's not really a big deal. Is it all developers, or all users, or anyone in the world? That might be a really big deal!

So in this challenge, we'd like you to get comfortable finding who has access to the _interesting thing_. For now, the interesting thing is just another secret in secretsmanager, but in later challenges, and on cloud penetration tests, this approach will become quite important.

So, go find out who has access to `DomainAdministrator-Credentials` in `secretsmanager` and then use your `cloudfoxable` profile to access that role and grab the secret.

**Cleanup Tasks**

None required

## Needles

### Overview

- **Author:** [Seth Art](https://www.linkedin.com/in/sethart/) ([@sethsec](https://x.com/sethsec))
- **Default state:** Enabled
- **Estimated Cost:** None
- **Starting Point:** `arn:aws:iam::ACCOUNT_ID:role/ramos`

**CloudFoxable Setup**

Create profile for `ramos`

```
[profile ramos]
region = us-west-2
role_arn = arn:aws:iam::ACCOUNTID:role/ramos
source_profile = cloudfoxable
```

**Challenge Details**

You've just gained access to the role `ramos`. This role has a bunch of read only access? Can you comb through the access you have and the resources that exist and see if you can find the flag?

**Cleanup Tasks**

None required

### Notes

Ran `cloudfox` with `all-checks` to see what resulted:

```
cloudfox aws -p cloudfoxable all-checks -v2
```

Reviewing output files in `loot` resulted in finding the flag in `cloudformation-data.txt`.

## Root

### Overview

- **Author:** [Seth Art](https://www.linkedin.com/in/sethart/) ([@sethsec](https://x.com/sethsec))
- **Default state:** Enabled
- **Estimated Cost:** No cost
- **Starting Point:** `arn:aws:iam::ACCOUNT_ID:role/Kent`

**CloudFoxable Setup**

Create profile for `Kent`

```
[Kent]
region = us-west-2
role_arn = arn:aws:iam::533267344535:role/Kent
source_profile = cloudfoxable
```

**Challenge Details**

You've just gained access to the role `Kent`. Can you get to the `root` flag in the SSM parameter store?

**Cleanup Tasks**

None required

### Notes

Ran `cloudfox` with `all-checks` to see what resulted:

```
cloudfox aws -p cloudfoxable all-checks -v2
```


```
cloudfox aws -p cloudfoxable role-trusts -v2
```

![[Pasted image 20240526183125.png]]

Assume into Beard then Lasso:

```
[Beard]
region = us-west-2
role_arn = arn:aws:iam::533267344535:role/Beard
source_profile = Kent

[Lasso]
region = us-west-2
role_arn = arn:aws:iam::533267344535:role/Lasso
source_profile = Beard
```

As Lasso I wanted to fetch the `root` related SSM parameter with the following command: 

```
aws --profile Lasso --region us-west-2 ssm get-parameter --with-decryption --name /production/CICD/root
```

The output from the command reveals the flag. 

## Segue

### Overview

- **Author:** Gerben Kleijn (Bishop Fox)
- **Default state:** Enabled
- **Estimated Cost:** $0.00/month
- **Starting Point:** `arn:aws:iam::ACCOUNT_ID:role/reinier`

**CloudFoxable Setup**

None required.

**Challenge Details**

You've just gained access to the reinier role. Utilize cloudfox and see where it takes you!

**Cleanup Tasks**

None required.

### Notes

Ran `cloudfox` with `all-checks` to see what resulted:

```
cloudfox aws -p cloudfoxable all-checks -v2
```

Reviewed the output. 

Reviewed `role-trusts-principals`:

```
cat role-trusts-principals.csv
```

![[Pasted image 20240526230842.png]]

Noticing that `reinier` can assume `deployment_automation`. 


```
# ---------------------------
# Queue: internal_message_bus
# ---------------------------
# Receive a message from the queue 

# WARNING: The following command can cause adverse effects in the environment. In production environments, use this command with caution
# WARNING: and in coordination with application owners. Receiving a message does not delete it from the queue, but this action
# WARNING: can potentially cause latency or it could DoS applications that consume the queue messages

aws --profile $profile --region us-west-2 sqs receive-message --queue-url  https://sqs.us-west-2.amazonaws.com/533267344535/internal_message_bus --attribute
-names All --message-attribute-names All --max-number-of-messages 5 --visibility-timeout 0

# Send a message to the queue without attributes file

# WARNING: The following command can cause adverse effects in the environment. Like fuzzing a web application, if you inject
# WARNING: malicious data you might find a vulnerability, but you also might break something. Unless you really know how the
# WARNING: messages are consumed, you should leave fuzzing to non-production environments.
aws --profile $profile --region us-west-2 sqs send-message --queue-url https://sqs.us-west-2.amazonaws.com/533267344535/internal_message_bus --message-body 
"[INSERT MESSAGE BODY]"


# Send message to the queue with attributes file (You'll have to create and populate the file)

# WARNING: The following command can cause adverse effects in the environment. Like fuzzing a web application, if you inject
# WARNING: malicious data you might find a vulnerability, but you also might break something. Unless you really know how the
# WARNING: messages are consumed, you should leave fuzzing to non-production environments.
aws --profile $profile --region us-west-2 sqs send-message --queue-url https://sqs.us-west-2.amazonaws.com/533267344535/internal_message_bus --message-body 
"[INSERT MESSAGE BODY] --message-attributes file://./file.json"
```


```
# ---------------------
# Queue: process_orders
# ---------------------
# Receive a message from the queue 

# WARNING: The following command can cause adverse effects in the environment. In production environments, use this command with caution
# WARNING: and in coordination with application owners. Receiving a message does not delete it from the queue, but this action
# WARNING: can potentially cause latency or it could DoS applications that consume the queue messages

aws --profile $profile --region us-west-2 sqs receive-message --queue-url  https://sqs.us-west-2.amazonaws.com/533267344535/process_orders --attribute-names
 All --message-attribute-names All --max-number-of-messages 5 --visibility-timeout 0

# Send a message to the queue without attributes file

# WARNING: The following command can cause adverse effects in the environment. Like fuzzing a web application, if you inject
# WARNING: malicious data you might find a vulnerability, but you also might break something. Unless you really know how the
# WARNING: messages are consumed, you should leave fuzzing to non-production environments.
aws --profile $profile --region us-west-2 sqs send-message --queue-url https://sqs.us-west-2.amazonaws.com/533267344535/process_orders --message-body "[INSE
RT MESSAGE BODY]"


# Send message to the queue with attributes file (You'll have to create and populate the file)

# WARNING: The following command can cause adverse effects in the environment. Like fuzzing a web application, if you inject
# WARNING: malicious data you might find a vulnerability, but you also might break something. Unless you really know how the
# WARNING: messages are consumed, you should leave fuzzing to non-production environments.
aws --profile $profile --region us-west-2 sqs send-message --queue-url https://sqs.us-west-2.amazonaws.com/533267344535/process_orders --message-body "[INSE
RT MESSAGE BODY] --message-attributes file://./file.json"
```

Check the SQS stuff related to the files.

```
┌──(kali㉿kali)-[~/…/aws/reinier-533267344535/loot/sqs-policies]
└─$ cat internal_message_bus.json 
{"Version":"2012-10-17","Id":"sqspolicy","Statement":[{"Sid":"First","Effect":"Allow","Principal":"*","Action":["sqs:SendMessage","sqs:ReceiveMessage"],"Resource":"arn:aws:sqs:us-west-2:533267344535:internal_message_bus","Condition":{"IpAddress":{"aws:SourceIp":"98.244.90.5/32"}}}]}  
```

```
┌──(kali㉿kali)-[~/…/aws/reinier-533267344535/loot/sqs-policies]
└─$ cat process_orders.json      
{"Version":"2012-10-17","Id":"terraform_queue","Statement":[{"Sid":"terraform_queue","Effect":"Allow","Principal":{"AWS":"*"},"Action":"sqs:SendMessage","Resource":"arn:aws:sqs:us-west-2:533267344535:process_orders"}]}
```




## The Topic is Execution

## Double Tap

## Pain

# Exploit Blurred Lines

## Trust Me

## Middle

# Exploit Public-Facing Application

## Furls 1

### Overview

- **Author:** [Seth Art](https://www.linkedin.com/in/sethart/) ([@sethsec](https://x.com/sethsec))
- **Default state:** Enabled
- **Estimated Cost:** No cost
- **Starting Point:** `arn:aws:iam::533267344535:role/ctf-starting-user`

**CloudFoxable Setup**

None required

**Challenge Details**

A Lambda function URL can be used to expose a Lambda function to the internet without an API gateway or another load balancer. This is really handy for builders, but can also be really handy for offensive security folk, as it's ripe for misconfiguration.

Use cloudfox to find the `furls1` FunctionURL and find the flag.

**Cleanup Tasks**

None required

### Notes:

Check commands on Cloudfox Documentation and first step I run is:

```
cloudfox aws -p cloudfoxable resource-trusts -v2
```

Found the `furls1` FunctionURL mention:

![[Pasted image 20240526205359.png]]

Google how to invoke a Lambda URL.

```
aws --profile cloudfoxable lambda invoke --function-name furls1 furls1-output.file
```

Not authorized to invoke this function.

Run `all-checks` to get birds-eye view:

```
cloudfox aws -p cloudfoxable all-checks -v2
```

After running the command, navigate to `loot` and review date:

![[Pasted image 20240526210603.png]]

Checking `endpoints-UrlsOnly.txt` reveals two publicly accessible URLs, one of which reveals the flag when visited in a web browser. 
## Search 1

## Furls 2

### Overview

**Overview**

- **Author:** [Seth Art](https://www.linkedin.com/in/sethart/) ([@sethsec](https://x.com/sethsec))
- **Default state:** Enabled
- **Estimated Cost:** No cost
- **Starting Point:** `arn:aws:iam::533267344535:role/ctf-starting-user`

**CloudFoxable Setup**

None required

**Challenge Details**

This Lambda Function URL doesn't just give you the key like `furls1`. You have to work a little harder for this one. CloudFox can definitely help you here.

**Cleanup Tasks**

None required

### Notes

Will reuse data from `all-checks` output previously.

The other lambda URL was `https://xntgsnlasc6lhoeytuwb5i4som0zzkfw.lambda-url.us-west-2.on.aws/` from:

![[Pasted image 20240526210603.png]]

The output from visiting the URL is:

![[Pasted image 20240526211145.png]]

We will need some authentication parameters it seems. 

Checking previous loot I look into the `lambda-get-function-commands.txt` file:

```
# Get function metadata including download location
aws --profile $profile --region us-west-2 lambda get-function --function-name auth-me
# Download function code to to disk (requires jq and curl) 
mkdir -p ./lambdas/auth-me
url=`aws --profile $profile lambda get-function --region us-west-2 --function-name auth-me | jq .Code.Location | sed s/\"//g` && curl "$url" -o ./lambdas/auth-me.zip
```

This line looks intriguing, as the function is called `auth-me`. I will download it and review its contents for anything interesting. 

Unfortunately the user does not have access to GetFunction. 





## The Topic is Exposure

## Search 2