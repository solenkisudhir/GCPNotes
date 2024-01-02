# Organizations

Account management service which allows you to consolidate multiple AWS accounts into an **organization** that you can centrally manage.

## Links
* [Video - Applying AWS Organisations to Complex Account Structures](https://www.youtube.com/watch?v=pfetMIlo_2s)
* [Slidedeck - Applying AWS Organisations to Complex Account Structures](https://www.slideshare.net/AmazonWebServices/hands-on-lab-introduction-to-microsoft-sql-server-in-aws-april-2017-aws-online-tech-talks-75477861)
* [Video - How Capital One Applies AWS Organizations Best Practices to Mana](https://www.youtube.com/watch?v=ZKpkF17d0Oo)
* [Documentation - How to use AWS Organizations to automate end to end Account creation](https://aws.amazon.com/blogs/security/how-to-use-aws-organizations-to-automate-end-to-end-account-creation/)
* [Documentation/Concept - Account Vending Machine](https://aws.amazon.com/answers/aws-landing-zone/)

## Key Benefits

### Centrally manage policies across accounts
* Instead of creating and managing policies across each individual account we can does this centrally

### Create new accounts at scale
* The limitation we've seen before in organisations has been lifted, we no longer need to wait for lengthy waits while account configuration is applied, such as billing.
* Can automate and programmatically create accounts

### View Charges and Usage across accounts
* Look at expenditure across all accounts, in one place.

## Key Concepts

### Organisation
* It is an *Entity*, it has an *ID* that can be referenced
* This is the umbrealla under which you consolidate all the *accounts* you wish to fully manage
* Ability to nest OUs to a depth of five levels, providing flexibility in structuring.

### Administrative Root
* Starting point for organising all your *accounts* to create *OUs* and organise them
* Top-most container in your *organization's* hierarchy

### Master account
* The *account* in which you create and manage your *organisation*
  * Creation of other accounts
  * Invite and manage invitations for other accounts
  * Remove accounts
  * Attach policies to entites such as *Roots*, *Organizational Units (OUs)*, and *accounts*
* It is the *payer account*
* You cannot change which *Account* is the master account so please choose carefully.

### Member account
* *Accounts* which are managed under the umbrella of the *organisation*, these are the "regular" *accounts*
* Can be an existing account which is invited into the *Organisation*
* Can only belong to one *Organization* at a time
* They can be programmatically created due to centralisation benefits, such as billing/payer configuration which would normally be a long and manual process. We now have inheritance

### Organisational Unit
* Is a logical grouping of *accounts* within an *organisation*.
* An OU can contain other OUs, up to five deep, to create hierarchys

#### Nesting
* Enables smaller units of management

### Service Control Policy
* A specific type of policy for *Organizations*
* Defines the AWS *service* actions that are accessible for use in different *accounts* in the *organization*
* Can be places on the *Administrative Root* (not recommended by AWS), *Organizational Units (OUs)*, and *Accounts*
* An empty *Service Control Policy* acts the same way an empty *IAM Policy* acts, it denies all by default

#### What if I have an SCP controlling the account in question at the same time as an "Full Administrative" policy attached to a User in the same account?
* The overall permissions granted to the Principal in question, say an *IAM Role* is the intersection of what is allowed by the *SCP* and the permissions granted by the *Policy* attached to the IAM Role.

#### Policy Overview
* Policies:
  * They are either a **blacklist** or a **whitelist**
  * Same rules and grammer as IAM policies except for:
    * Cannot specify *conditions*
    * The *resource* section **must** be equal to "*"
* *SCPs* allow you to control which Service actions are accessible to *principles* in the accounts of your *organizatoin*, they can be:
  * Account Root
  * IAM Users
  * IAM Roles

* Can be placed on the Root, the OU, and on an Account and all three at once if needed.
  * An example could be applying a broad level of grants and denies at the Root, defining a little more at the OU, and then getting granular on the Account level.


## Core Features

### Centralised management of all your AWS accounts
* You can **create** accounts automatically that are part of your org and or **invite** other accounts to join your org.
* Attach policies that affect some or all of the accounts.
  * Question and find out more ^

### Consolidated billing for all member accounts
* Use the *master* account of the org to consolidate and pay for all member accounts.

### Hierarchical grouping of your accounts to meet budgetart, security, or compliance needs
* Accounts can be grouped into *Organisational Units (OUs)*, these *OUs* can have *policies* attached to restrict or grant access to certain AWS Services, in the event a regulatory requirements comes into place.

### Security Controls for Services and API actions
* An *administrator* within the *master* account of an org can restrict which *Services* and individual *API actions* the users and roles in each member account can access by using *Service Control Policies (SCPs)*.
  * This means that an *administrator* within a *master* account can override the administrators of member accounts.
  * tl;dr Org perms overrule account perms
  
### Integration and support for IAM
* *Organisations* expand the control of *IAM* to the *account* level by giving you control over what *users* and *roles* in an *account* or a *group* can do.
* tl;dr The *user*/*role* can access only what is allowed by **both** the Organisations policies and IAM policies, if either blocks an operation, the *user*/*group* can't access that operation.

## Examples

### Architectural

#### Complex Organisational Structure 
![org reference architecture](https://image.slidesharecdn.com/18-170427182706/95/applying-aws-organizations-to-complex-account-structures-april-2017-aws-online-tech-talks-12-638.jpg?cb=1493747779)

### Policies

#### Whitelist example
```
{
    "Version":"2012-10-17",
    "Statement":[
        {
            "Effect":"Allow",
            "Action":["EC2:*","S3:*"],
            "Resource":"*"
        }
    ]
}
```

#### Blacklist example
``` 
    "Version":"2012-10-17",
    "Statement":[
        {
            "Effect":"Allow",
            "Action": "*:*",
            "Resource":"*"
        },
        {
            "Effect":"Deny",
            "Action":"S3:PutObject",
            "Resource":"*"
        }
    ]
}
```