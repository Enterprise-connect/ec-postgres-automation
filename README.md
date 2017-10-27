# ec-postgres-automation
 Enterprise-Connect Postgres Reach-in Automation

### Scenario

*Connect to your DB instance in Predix from outside the Predix Cloud using Enterprise Connect.*

[Usage scenario three- Simulating Server-via-Gateway Two-way Bi-directional Virtual Connectivity](https://github.com/Enterprise-connect/ec-sdk/wiki/EC-Agent)

```
┌──────────────────────────────────────┐               
│             Predix Cloud             │
│                                      │
│  ┌───────────────┐ ┌───────────────┐ │                         ┌───────────────┐
│  │ Enterprise    │ │ Database      │<--Virtual Connectivity--->│   Client      │
│  │ Connect       │ │ Postgres      │ │                         │               │
│  └───────────────┘ └───────────────┘ │                         └───────────────┘
└──────────────────────────────────────┘
```

### Prerequisites

- OSX
- jq  https://stedolan.github.io/jq/download/
- cf cli https://github.com/cloudfoundry/cli
- Login to CF Cli and target the org/space where the EC instance was created
- UAA client with the correct scope for your EC instance

### Steps


```
$ git clone https://github.com/Enterprise-connect/ec-postgres-automation.git
$ cd ec-postgres-automation
$ chmod +x suggestECConfig.sh
```

*Usage*
```
./suggestECConfig.sh -e <EC Service Name> -c <Client ID> -s <Client Secret> -d <Refresh token duration> -r <Resource Host> -p <Resource Port#> -l <Local Port>  [-x <Proxy URL> -y <Flag to push to Predix>]

Parameters:
  -e <EC Service Name>
  -c <Client ID>
  -s <Client Secret>
  -d <Refresh token duration>
  -r <Resource Host>
  -p <Resource Port#>
  -l <Local Port>
  -x <Proxy URL>   (Optional)
  -y <Flag to push to Predix, y/n>
```

Example:

```
./suggestECConfig.sh -e "support-ec-instance" -c "client1" -s "secret1" -d "3000" -r "db-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX.XXXXXXXXXXXX.us-west-2.rds.amazonaws.com" -p "5432" -l "7990" -x "http://proxy-src.research.ge.com:8080" -z y
```

###Output

After running the script, it will create the following files:

GATEWAY
```
./gateway-<EC-GUID>/
total 15680
-rwxr-xr-x  1 502670911  1515967849   7.6M Oct 18 13:28 ecagent_linux_sys
-rw-r--r--  1 502670911  1515967849   231B Oct 19 10:58 manifest.yml
-rw-r--r--  1 502670911  1515967849   288B Oct 19 10:58 ec.sh
```
SERVER
```
./server--<EC-GUID>/
total 15680
-rwxr-xr-x  1 502670911  1515967849   7.6M Oct 18 13:28 ecagent_linux_sys
-rw-r--r--  1 502670911  1515967849   230B Oct 19 10:58 manifest.yml
-rw-r--r--  1 502670911  1515967849   565B Oct 19 10:58 ec.sh
```
CLIENT
```
./client--<EC-GUID>/
total 15480
-rwxr-xr-x  1 502670911  1515967849   7.6M Oct 18 13:28 ecagent_darwin_sys
-rw-r--r--  1 502670911  1515967849   427B Oct 19 10:59 ec.sh
```

The final result looks like this:

```
┌──────────────────────────────────────────────┐               
│                Predix Cloud                  │
│ ┌────────────────────────────────────┐       │                   
│ │             Services               │       │
│ │ ┌───────────────┐ ┌───────────────┐│       │
│ │ │ Enterprise    │ │ Database      ││       │
│ │ │ Connect       │ │ Postgres      ││       │
│ │ └───────────────┘ └───────────────┘│       │
│ └───────│──────────────────│─────────┘       │
│         │                 TCP                │
│ ┌───────│──────────────────│─────────┐       │
│ │       │     Applications │         │       │
│ │ ┌───────────────┐ ┌───────────────┐│       │
│ │ │ EC            │ │ EC            ││       │
│ │ │ Gateway       │ │ Server        ││       │
│ │ └───────────────┘ └───────────────┘│       │         ┌───────────────────────┐
│ └───────│──────────────────│─────────┘       │         │      Client Env       │
│         └───────WSS────────┘                 │         │  ┌───────────────────┐│
│         │                                    │         │  │                   ││
│         └──────────────────WSS───────────────+─────────+──│   EC Client       ││
│                                              │         │  │                   ││
└──────────────────────────────────────────────┘         │  └───────────────────┘│
                                                         └───────────────────────┘
```
