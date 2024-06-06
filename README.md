## Accelbyte MatchMaking v2 CLI Tool

### Summary
The MatchMaking v2 CLI Tool (`mmtoolcli`) was built to help with testing a developer's MatchMaking v2 Services
by going through specific flows specified by the user.

### Setup

The following are required to be created before this tool will function properly:

- namespace
- matchpool
- session template
- OATUH Client
- mmtoolconfig.yaml

#### _namespace_
This will be the location of where the match pool and users will be used from.

#### _matchpool_
The MatchFunction (and other settings) here will be used during each flow

#### _session template_
This is the template that will be used when interacting with party specified flows

#### _OAUTH Client_
This is the OAUTH Client that will be used throughout the entire tool. 
You will need:
    
1. Client ID and Secret

2. The following permissions are needed on the Client

```
  ADMIN:NAMESPACE:*:USER:*	Create, Read, Update, Delete

  ADMIN:NAMESPACE:*:CLIENT	Read

  NAMESPACE:*:MATCHMAKING:TICKET	Create

  ADMIN:NAMESPACE:*:DSNOTIFICATION	Create

  NAMESPACE:*:SESSION:GAME	Create, Read, Update, Delete

  ADMIN:NAMESPACE:*:USER:*:STATITEM	   Create, Read, Update, Delete

  NAMESPACE:*:SESSION:PARTY	Read

  ADMIN:NAMESPACE:*:INFORMATION:USER:* Delete
```

#### _mmtoolconfig.yaml_
By default, this tool looks for a `mmtoolconfig.yaml` in your `$HOME` directory. You can also load your own config.

```
mmtoolcli -h 
Flags:
  -c, --config string   config file (default is $HOME/mmtoolconfig.yaml)
```
Below is an example config which you can fill in your own values. 

```
#application
polltime: 3
canceltime: 60

#accelbyte sdk vars
abclientid:
abclientsecret:
abbaseurl: <https://your-env.accelbyte.io>

#user creation params
userprefix: userPrefix #this will end up looking like userPrefix<generatedUUID> -- userPrefix203948230hasf8
useremaildomain: somedomain.com #no @ required
userdob: "1985-01-12" #DoB must be over 21 years of age
userpassword:
usercountry: US

statcodes:
  - name: mmr
    minvalue: 40
    maxvalue: 60
```

This config file is meant to drive the functionality that is consistently needed throughout the tool and provide
easier repeatability and organization, as well as store environment variables.

#### _MatchTicketAttributes_

You can add MatchTicketAttributes by using a JSON file from the command line. An example of this would look like:

```
{
  "LOCALE": "us-NA",
  "VERSION": "0.2.3.hello.world"
}
```

An example of the command to load these attributes would look like:

`mmtool-cli tickets create solo -n <name of namespace> -m <matchpool name> -u 2 -a <./path/to/attributes/file.json>`

### Example Usages

#### _Generate Solo User Tickets_
If you wish to create Match Tickets that have only 1 User on them, the `solo` command is what you'll want to use. Below are examples of ways to use this command.

REQUIRED FLAGS:
```
  -m, --matchpool string   The MatchPool you want to run MatchMaking V2 on: REQUIRED
  -n, --namespace string   The Namespace where your MatchPool is located: REQUIRED
  --deleteusers bool         Should the the Users used be deleted? Default TRUE
  --leavesession bool        Should users leave the sessions they joined? Default TRUE
  --deletetickets bool       Should the match tickets created be deleted? Default TRUE
```

OPTIONAL FLAGS:
```
  -f, --file string              Load users from a yaml file
  -a, --ticketattributes string  Load match ticket attributes from a JSON file
  --deleteusers bool         Should the the Users used be deleted? Default TRUE
  --leavesession bool        Should users leave the sessions they joined? Default TRUE
  --deletetickets bool       Should the match tickets created be deleted? Default TRUE
```
**_New Users:_**

`mmtool-cli tickets create solo -n <namespace> -m <matchpool> -u <INT number of users>`

_**From File:**_

`mmtool-cli tickets create solo -n <name of namespace> -m <matchpool name> -f <./path/to/users/file.yaml>`

#### _Generate Party User Tickets_
If you wish to create Match Tickets in which Users are considered in a Party, the `party` command is what you'll want to use. Below are examples of ways to use this command.

REQUIRED FLAGS:

```
  -f, --file string              Load users from a yaml file
  -a, --ticketattributes string  Load match ticket attributes from a JSON file
  -m, --matchpool string         The MatchPool you want to run MatchMaking V2 on: REQUIRED
  -n, --namespace string         The Namespace where your MatchPool is located: REQUIRED
  -p, --partysize int            Amount of Users you want to create: REQUIRED
  -s, --sessiontemplate string   The Session Template used for creating a Party: REQUIRED
```

OPTIONAL FLAGS:

```
  --deleteusers bool         Should the the Users used be deleted? Default TRUE
  --leavesession bool        Should users leave the sessions they joined? Default TRUE
  --deletetickets bool       Should the match tickets created be deleted? Default TRUE
  --gamesession bool         Should the match tickets created using game session instead of party session? Default FALSE
```

**_New Users_**

`mmtool-cli tickets create party -m <matchpool name> -n <namespace name> -p <INT size of party> -s <session template name> -u <INT users to create>`

**_From File:_**

`mmtool-cli tickets create party -m <matchpool name> -n <namespace name> -p <INT size of party> -s <session template name> -f ./path/to/users/file.yaml>`


#### _Create Users And Write to a File_
If you do not have pre-made users, you can use this command to create users and write them to a file to be read from.
```
  -f, --filename string    The name of the file that will be created, or overriden: REQUIRED
  -n, --namespace string   The Namespace where your MatchPool is located: REQUIRED
  -u, --users int          Amount of Users you want to create: REQUIRED
```

`mmtool-cli users create -n <namespace name> -u <INT users to create> -f <name of the file>`
