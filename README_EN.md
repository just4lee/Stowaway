![stowaway.png](https://github.com/ph4ntonn/Stowaway/blob/master/img/logo.png)

# Stowaway

[![GitHub issues](https://img.shields.io/github/issues/ph4ntonn/Stowaway)](https://github.com/ph4ntonn/Stowaway/issues)
[![GitHub forks](https://img.shields.io/github/forks/ph4ntonn/Stowaway)](https://github.com/ph4ntonn/Stowaway/network)
[![GitHub stars](https://img.shields.io/github/stars/ph4ntonn/Stowaway)](https://github.com/ph4ntonn/Stowaway/stargazers)
[![GitHub license](https://img.shields.io/github/license/ph4ntonn/Stowaway)](https://github.com/ph4ntonn/Stowaway/blob/master/LICENSE)

Stowaway is a multi-hop proxy tool for security researchers and penetration testers.

Stowaway lets you proxy external traffic through multiple hops into a target internal network, bypassing access restrictions, building a tree-like node topology, and managing it easily.

Thanks for the stars. Issues and bug reports are welcome. :kissing_heart:

**Please be sure to read the usage guide and the notes at the end before using.**

## Disclaimer:

> This project is intended for cybersecurity research and educational purposes only. Any unauthorized or malicious use is strictly prohibited. Before conducting any testing, please ensure you have explicit authorization from the target system and fully comply with all applicable laws and regulations in your country or region.
The user assumes full responsibility for any direct or indirect consequences resulting from the use of this tool, including but not limited to data loss, system damage, or legal issues. The author of this project does not accept any liability for misuse or illegal use of this tool.
By using this tool, you acknowledge that you have read, understood, and agreed to the full contents of this disclaimer.

## Features

- User-friendly interaction with command auto-completion and history search
- Clear node topology
- Clear node information display
- Active/passive connections between nodes
- Reconnection support between nodes
- Nodes can connect via SOCKS5/HTTP proxies
- Nodes can connect via SSH tunnels
- TCP/HTTP/WS can be selected for inter-node traffic
- Multi-hop SOCKS5 proxying with UDP/TCP and IPv4/IPv6 support
- Nodes can access arbitrary hosts via SSH
- Remote shell
- File upload/download
- Local/remote port mapping
- Port reuse
- Start/stop services as needed
- Mutual authentication between nodes
- Traffic encryption with TLS/AES-256-GCM
- Compared with v1.0, the file size is reduced by 25%
- Multi-platform support (Linux/Mac/Windows/MIPS/ARM)

## Build and Demo

- Use `make` to directly compile programs for multiple platforms, or refer to the Makefile for compiling specific programs.
- If you prefer not to compile, you can directly use the programs available on the [release](https://github.com/ph4ntonn/Stowaway/releases) page
- Demo video: [YouTube](https://www.youtube.com/watch?v=Lh5Q0RPWKMU&list=PLkbGxnHFIhA_g5XZtKzN4u-JXRq41L2g-)

## Usage

### Roles

Stowaway has two roles:
- `admin`  The controller used by the operator
- `agent`  The node deployed on a target host

### Noun definition

- Node: Either `admin` or `agent`
- Active mode: The current node actively connects to another node
- Passive mode: The current node listens on a specific port and waits for another node to connect
- Upstream: Traffic between the current node and its parent node
- Downstream: Traffic between the current node and **all** of its child nodes

### Quick start

The following commands quickly start the simplest Stowaway setup:

- admin: `./stowaway_admin -l 9999`
- agent: `./stowaway_agent -c <stowaway_admin's IP>:9999`

### Parameter analysis

- admin

```
Parameter:
-l Listening address in passive mode [ip]:<port>
-s Shared secret for node communication; must be the same on all nodes (admin and agent)
-c Target node address under active mode
--socks5-proxy SOCKS5 proxy server address
--socks5-proxyu SOCKS5 proxy server username
--socks5-proxyp SOCKS5 proxy server password
--http-proxy HTTP proxy server address
--down Downstream protocol type, default is raw TCP traffic, optional HTTP/WS
--tls-enable Enable TLS for node communication, after enabling TLS, AES encryption will be disabled
--domain Specify the TLS SNI/WebSocket domain name. If it is empty, it defaults to the target node address
--heartbeat Enable heartbeat packets
```

- agent

```
Parameter:
-l Listening address in passive mode [ip]:<port>
-s Node communication encryption key
-c Target node address under active mode
--socks5-proxy SOCKS5 proxy server address
--socks5-proxyu SOCKS5 proxy server username (optional)
--socks5-proxyp SOCKS5 proxy server password (optional)
--http-proxy HTTP proxy server address
--reconnect Reconnect time interval
--rehost The IP address to be reused
--report The port number to be reused
--up Upstream protocol type, default is raw TCP traffic, optional HTTP/WS
--down Downstream protocol type, default is raw TCP traffic, optional HTTP/WS
--cs Console encoding (default: utf-8; optional: gbk)
--tls-enable Enable TLS for node communication, after enabling TLS, AES encryption will be disabled
--domain Specify the TLS SNI/WebSocket domain name. If it is empty, it defaults to the target node address.
```

### Parameter usage

#### -l

This parameter can be used on admin&&agent, under passive mode 

If you do not specify an IP address, it will default to listening on `0.0.0.0`

- admin:  `./stowaway_admin -l 9999` or `./stowaway_admin -l 127.0.0.1:9999`

- agent:  `./stowaway_agent -l 9999`  or `./stowaway_agent -l 127.0.0.1:9999`

#### -s

This parameter can be used on admin&&agent, under both active && passive mode

This parameter is optional. If it is left blank, it means that the communication will not be encrypted. Conversely, if a key is provided by the user, the communication will be encrypted based on that key.

- admin:  `./stowaway_admin -l 9999 -s 123`

- agent:  `./stowaway_agent -l 9999 -s 123`

#### -c

This parameter can be used on admin&&agent, under active mode 

It represents the address of the node you wish to connect to

- admin:  `./stowaway_admin -c 127.0.0.1:9999`

- agent:  `./stowaway_agent -c 127.0.0.1:9999` 

#### --socks5-proxy/--socks5-proxyu/--socks5-proxyp/--http-proxy

These four parameters can be used on admin&&agent , under active mode

`--socks5-proxy` represents the address of the socks5 proxy server, `--socks5-proxyu` and `--socks5-proxyp` are optional

`--http-proxy` represents the address of the http-proxy server, the usage is the same as socks5

Without username and password:

- admin:  `./stowaway_admin -c 127.0.0.1:9999 --socks5-proxy xxx.xxx.xxx.xxx`

- agent:  `./stowaway_agent -c 127.0.0.1:9999 --socks5-proxy xxx.xxx.xxx.xxx`

Require username and password:

- admin:  `./stowaway_admin -c 127.0.0.1:9999 --socks5-proxy xxx.xxx.xxx.xxx --socks5-proxyu xxx --socks5-proxyp xxx`

- agent:  `./stowaway_agent -c 127.0.0.1:9999 --socks5-proxy xxx.xxx.xxx.xxx --socks5-proxyu xxx --socks5-proxyp xxx`

#### --up/--down

These two parameters can be used on admin&&agent, under active && passive mode

However, note that there is no `--up` parameter on the admin

These two parameters are optional. If left empty, upstream/downstream traffic will use raw TCP.

If you wish for the upstream/downstream traffic to be HTTP/WS traffic, simply set these two parameters to `http` or `ws`

- admin:  `./stowaway_admin -c 127.0.0.1:9999 --down ws` 

- agent:  `./stowaway_agent -c 127.0.0.1:9999 --up ws`  or `./stowaway_agent -c 127.0.0.1:9999 --up ws --down ws`

Two more notes:

First, once you set the upstream/downstream traffic of a node to TCP/HTTP/WS, the downstream/upstream traffic of its parent/child nodes must match.

Like this:

- admin:  `./stowaway_admin -c 127.0.0.1:9999 --down ws`

- agent:  `./stowaway_agent -l 9999 --up ws`

In this case, the agent must set `--up` to `ws`, otherwise it will cause network errors.

The rules between admin<-->agent are the same as agent<-->agent.

Assuming agent-1 is waiting for the connection of child nodes on the port `127.0.0.1:10000` and has set `--down ws`

Then, agent-2 must also set `--up` to `ws`, otherwise it will lead to network errors.

- agent-2:  `./stowaway_agent -c 127.0.0.1:10000 --up ws`

Second, HTTP is half-duplex and is not a great fit for Stowaway's full-duplex communication model. The HTTP option here only implements an HTTP-like message format, not a fully functional HTTP workflow. You can still use it, but traffic in HTTP message format cannot be forwarded by NGINX between Stowaway nodes. This code path is kept both for special cases and as a reference template for implementing custom transports.

If you need a reverse proxy such as NGINX, use the WebSocket (`ws`) transport (ideally with TLS).

#### --reconnect

This parameter can be used on the agent, under active mode.

This parameter is optional. If not set, the node will not automatically reconnect after a network disconnection. If set, the node will try to reconnect to its parent every x seconds (the number of seconds you set).

- admin:  `./stowaway_admin -l 9999`

- agent:  `./stowaway_agent -c 127.0.0.1:9999 --reconnect 10`

In this scenario, if the connection between the agent and the admin is interrupted, the agent will attempt to reconnect to the admin every ten seconds.

The rules between admin<-->agent are the same as agent<-->agent

Additionally, `--reconnect` can be used together with `--socks5-proxy`, `--socks5-proxyu`, `--socks5-proxyp`, or `--http-proxy`. In that case, the agent will attempt to reconnect through the proxy using the settings specified at startup.

#### --rehost/--report

These two parameters are used exclusively on the agent side. For details, see the port reuse section below.

#### --cs

This parameter can be used on the agent, under both active and passive mode.

This is mainly to fix garbled output in the `shell` feature. If the agent runs on a system whose console encoding is GBK (commonly Windows) while the admin runs with UTF-8 console encoding, you should set this parameter to `gbk`.

- Windows: `./stowaway_agent -c 127.0.0.1:9999 -s 123 --cs gbk`

#### --tls-enable

This parameter can be used on both admin and agent, under both active and passive mode.

By setting this option, traffic between nodes can be encrypted with TLS

- admin: `./stowaway_admin -l 10000 --tls-enable -s 123`
- agent: `./stowaway_agent -c localhost:10000 --tls-enable -s 123`

Please note that when this parameter is enabled, AES encryption will be disabled by default. The `-s` parameter will then be used only for mutual authentication between nodes and for port reuse.

Additionally, when this parameter is enabled, **ensure that every node in the network (including the admin) has this parameter enabled**

#### --domain

This parameter can be used on both admin and agent, under active mode.

This option lets you specify the TLS SNI name or the WebSocket host/domain name for the current node.

- admin: `./stowaway_admin -l 10000 --tls-enable -s 123`
- agent: `./stowaway_agent -c xxx.xxx.xxx.xxx:10000 --tls-enable -s 123 --domain xxx.com`

#### --heartbeat

This parameter can be used on the admin, under both active and passive mode.

When enabled, the admin continuously sends heartbeat packets to the first node, keeping the connection alive even when a reverse proxy sits in between.

Assuming there are reverse proxy devices similar to NGINX between the admin and agent, proxying port 8080 to port 8000, an example is as follows:
- admin: `./stowaway_admin -l 8000 --tls-enable -s 123 --down ws --heartbeat`
- agent: `./stowaway_agent -c xxx.xxx.xxx.xxx:8080 --tls-enable -s 123 --domain xxx.com --up ws`

## Port reuse

Stowaway currently supports port reuse based on SO_REUSEPORT/SO_REUSEADDR, as well as port reuse based on iptables.

- On Linux, Stowaway can reuse most ports.
- On Windows, it cannot reuse some service ports such as IIS/RDP, but can reuse MySQL/Apache ports and more.

### How To?

- SO_REUSEPORT/SO_REUSEADDR

  Assuming that the agent uses the port reuse mechanism to reuse port 80

  To do this, the agent must set `--rehost`&&`--report`&&`-s` at startup.

  - `--rehost` This represents the desired IP address for reuse, which cannot be `0.0.0.0` and generally should be the external address of the network card

  - `--report` This represents the desired port for reuse

  - `-s` This represents the communication key (shared secret)

  **This mode mainly targets Windows and macOS. Linux may also work, but with more restrictions.**

  - admin：`./stowaway_admin -c 192.168.0.105:80 -s 123`
  - agent： `./stowaway_agent  --report 80 --rehost 192.168.0.105 -s 123`

- IPTABLES

  Assuming that the agent uses the port reuse mechanism to reuse port 22

  To do this, the agent must set `-l`&&`--report`&&`-s` at startup.

  - `-l` This represents the port that cannot be accessed normally, meaning the port you actually want the agent to listen on and accept connections

  - `--report` This represents the desired port for reuse

  - `-s` This represents the communication key

  **This method only supports Linux. The agent manipulates iptables automatically, and root privileges are required.**

  - agent： `./stowaway_agent --report 22 -l 10000 -s 123`

    After the agent starts, use the `reuse.py` script located in the `script` directory.

    Set the value of SECRET (the value of SECRET is the communication key, aka -s option)

    Then run: `python reuse.py --start --rhost xxx.xxx.xxx.xxx --rport 22`

    - `--rhost` This represents the address of the agent

    - `--rport` This represents the port being reused, which in this example should be 22.

  - The admin can then connect to this agent: `./stowaway_admin -c xxx.xxx.xxx.xxx:22 -s 123`

### Notice

- The scenarios mentioned above only enumerate the connection between the admin and the agent. The connection between agents also follows the same principles and has no differences.

- If the agent is terminated using `ctrl-c` or `kill`, the program will automatically clean up the iptables rules. However, if it is terminated using `kill -9`, the rules cannot be cleaned up automatically. To prevent leftover rules from blocking access to the reused service, run: `python reuse.py --stop --rhost xxx.xxx.xxx.xxx --rport xxx`. This disables the forwarding rules so the original service can be accessed normally.

- If using the IPTABLES mode for port reuse, it will forcefully listen on `0.0.0.0`, and cannot be specified using the `-l` parameter.

## How to build a multi-level network?

From the example above, you can see that only the admin and one agent are involved.

However, building a multi-level network is the core functionality of Stowaway.

In Stowaway, building a multi-level network requires the use of commands such as `listen`, `connect`, and `sshtunnel` within the admin interface

Example:

- admin: `./stowaway_admin -l 9999 -s 123` 

At this point, agent-1 has already connected to the admin

- agent-1:  `./stowaway_agent -c 127.0.0.1:9999 -s 123`

Suppose you also want to connect to agent-2:

- agent-2:  `./stowaway_agent -l 10000 -s 123`

Then, you can enter `use 0` -> `connect agent-2's IP:10000` in the admin console to add agent-2 to the network as a child node of agent-1.

Next, if you want to connect another node (agent-3) but cannot reach it from agent-1 directly:

Use `use 0` -> `listen` in the admin console, select `1.Normal Passive`, and enter `10001` so agent-1 listens on port 10001 and waits for agent-3 to connect.

After that, start agent-3 as follows:

- agent-3: `./stowaway_agent -c 127.0.0.1:10001 -s 123`

At this point, agent-3 has become another child node of agent-1 and joined the network.

For details on `listen` and `sshtunnel`, see the command reference below.

## How to reconnect?

Stowaway currently supports various methods of reconnection, summarized briefly as follows:

First, when a parent node goes offline, only one type of node will actively disconnect: nodes that were started in active mode and do not have reconnection enabled.

If reconnection is set up, the node will attempt to reconnect at specified time intervals.

Additionally, all nodes started in passive mode will not actively disconnect. Instead, they will re-listen on the specified port based on the parameters set at startup. Users can still use `connect` and `sshtunnel` to reconnect these nodes to the network.

## Some points you should know

1. **If a branch disconnects due to network fluctuations or a middle node going offline, when attempting to reconnect, it is essential to connect to the head node of the missing branch.** For example, after the admin, there is node1. Node1 branches into two: one branch is node1 -> node 2 -> node 3 -> node 4, and the other branch is node1 -> node 5 -> node 6. If node2 goes offline, node3 and node4 will remain active. If the user wishes to reconnect node3 and node4 to the network, there are two options available. Firstly, if node1 can directly access node3, the user can reconnect node3 to the network at any time by using the `connect` or `sshtunnel` commands on node1. It's important to note that even if node1 can also access node4 directly, please do not connect to node4 directly. Instead, connect to the head node of the missing chain (node3->node4), which is node3. This way, both node3 and node4 can be reconnected to the network. Alternatively, if node1 cannot directly access node3 (i.e., it must go through node2), please restart node2 and join it to the network first. Then, on node2, use the `connect` or `sshtunnel` commands to connect to node3, thus reconnecting both node3 and node4 to the network.
2. **When a node is offline, all `socks`, `backward`, and `forward` services related to that node and its child nodes will be forcibly stopped.**

## Command analysis

In the admin console, you can use Tab for command completion and the arrow keys (up, down, left, right) to browse command history or move the cursor.

The admin console is divided into two levels. The first level is the main panel, which includes the following commands:

- `help`: Display help information for the main panel

```
(admin) >> help
  help                                     		Show help information
  detail                                  		Display connected nodes' detail
  topo                                     		Display nodes' topology
  use        <id>                          		Select the target node you want to use
  exit                                     		Exit Stowaway
```

- `detail`: Display detailed information about online nodes

```
(admin) >> detail
Node[0] -> IP: 127.0.0.1:10000  Hostname: ph4ntoms-MBP.lan  User: ph4ntom
Memo:
```

- `topo`: Display the parent-child relationships of online nodes

```
(admin) >> topo
Node[0]'s children ->
Node[1]

Node[1]'s children ->
```

- `use`: Select a node

```
(admin) >> use 0
(node 0) >>
```

- `exit`: Exit stowaway

```
(admin) >> exit
[*] Do you really want to exit stowaway?(y/n): y
[*] BYE!
```

When you select a node with `use`, the admin enters the second level (node panel), which includes the following commands:

- `help`: Display the help information for the node panel

```
(node 0) >> help
  help                                            Show help information
  status                                          Show node status,including socks/forward/backward
  listen                                          Start port listening on current node
  addmemo    <string>                             Add memo for current node
  delmemo                                         Delete memo of current node
  ssh        <ip:port>                            Start SSH through current node
  shell                                           Start an interactive shell on current node
  socks      <lport> [username] [pass]            Start a socks5 server
  stopsocks                                       Shut down socks services
  connect    <ip:port>                            Connect to a new node
  sshtunnel  <ip:sshport> <agent port>            Use sshtunnel to add the node into our topology
  upload     <local filename> <remote filename>   Upload file to current node
  download   <remote filename> <local filename>   Download file from current node
  forward    <lport> <ip:port>                    Forward local port to specific remote ip:port
  stopforward                                     Shut down forward services
  backward    <rport> <lport>                     Backward remote port(agent) to local port(admin)
  stopbackward                                    Shut down backward services
  shutdown                                        Terminate current node
  back                                            Back to parent panel
  exit                                            Exit Stowaway 
```

- `status`: Display the socks/forward/backward status of the current node

```
(node 0) >> status
Socks status:
      ListenAddr: 0.0.0.0:10000    Username:    Password:
-------------------------------------------------------------------------------------------
Forward status:
      [1] Listening Addr: [::]:20000 , Remote Addr: 192.168.1.1:22 , Active Connections: 0
      [2] Listening Addr: [::]:30000 , Remote Addr: 192.168.1.1:22 , Active Connections: 0
-------------------------------------------------------------------------------------------
Backward status:
      [1] Remote Port: 40000 , Local Port: 50000 , Active Connections: 0
```

- `listen`: Instruct the node to listen on a specific port and wait for connection from child node

```
(node 0) >> listen
[*] MENTION! If you choose IPTables Reuse or SOReuse, you MUST CONFIRM that the node was initially started in the corresponding way!
[*] When you choose IPTables Reuse or SOReuse, the node will use the initial config(when node started) to reuse port!
[*] Please choose the mode(1.Normal passive / 2.IPTables Reuse / 3.SOReuse): 1
[*] Please input the [ip:]<port> : 10001
[*] Waiting for response......
[*] Node is listening on 10001
```

Note that `listen` is a special command. As you can see, the `listen` command has three modes

1. `Normal passive`: This option implies that the agent will listen on the target port in a normal way and wait for child nodes to connect.
2. `IPTables Reuse`：This option implies that the agent will reuse the port using IPTables and wait for child nodes to connect.
3. `SOReuse`：This option implies that the agent will reuse the port using SOReuse and wait for child nodes to connect.

The first mode is the most commonly used. If the parent node is listening in this way, child nodes only need to use `-c parent_node_ip:port` to join the network.

The second and third modes are rather unique. If the user selects the second or third mode, they must ensure that the node they are currently operating on has been started using port reuse. Otherwise, these two modes cannot be used.

In the second and third modes, users won't need to input any information. The node will automatically reuse the port using the parameters set at its own startup and prepare to accept connections from child nodes.

Furthermore, the `listen` command can only accept one child node connection at a time. If multiple child nodes need to connect, please execute the `listen` command the corresponding number of times.

- `addmemo`: Add a memo for the current node

```
(node 0) >> addmemo test
[*] Memo added!
(node 0) >> exit
(admin) >> detail
Node[0] -> IP: 127.0.0.1:10000  Hostname: ph4ntoms-MBP.lan  User: ph4ntom
Memo:  test
```

- `delmemo`: Delete the memo of the current node

```
(node 0) >> delmemo
[*] Memo deleted!
(node 0) >> exit
(admin) >> detail
Node[0] -> IP: 127.0.0.1:10000  Hostname: ph4ntoms-MBP.lan  User: ph4ntom
Memo:
```

- `ssh`: Instruct the node to establish an SSH connection to the target.

```
(node 0) >> ssh 127.0.0.1:22
[*] Please choose the auth method(1.username&&password / 2.certificate): 1
[*] Please enter the username: ph4ntom
[*] Please enter the password: *****
[*] Waiting for response.....
[*] Connect to target host via ssh successfully!
 # ph4ntom @ ph4ntoms-MBP in ~ 👑 [17:03:56]
$ whoami
ph4ntom
 # ph4ntom @ ph4ntoms-MBP in ~ 👑 [17:04:16]
$
```

Under this mode, the tab key will be disabled

- `shell`: Get the shell of the current node

```
(node 0) >> shell
[*] Waiting for response.....
[*] Shell is started successfully!

bash: no job control in this shell

The default interactive shell is now zsh.
To update your account to use zsh, please run `chsh -s /bin/zsh`.
For more details, please visit https://support.apple.com/kb/HT208050.
bash-3.2$ whoami
ph4ntom
bash-3.2$
```

Under this mode, the tab key will be disabled

- `socks`：Start the socks5 service on the current node

```
(node 0) >> socks 7777
[*] Trying to listen on 0.0.0.0:7777......
[*] Waiting for response......
[*] Socks start successfully!
(node 0) >>
```

Please note that the port 7777 is not opened on the agent, but rather on the admin

If you need to set a username and password, you can modify the above command to `socks 7777 <your username> <your password>`

If you need to specify the interface to listen on, you can modify the above command to `socks xxx.xxx.xxx.xxx:7777`

- `stopsocks`: Stop the SOCKS5 service on the current node

```
(node 0) >> stopsocks
Socks Info ---> ListenAddr: 0.0.0.0:7777    Username: <null>    Password: <null>
[*] Do you really want to shut down socks?(yes/no): yes
[*] Closing......
[*] Socks service has been closed successfully!
(node 0) >>
```

- `connect`: Instruct the current node to connect to another child node

```
agent-1: ./stowaway_agent -l 10002
```

```
(node 0) >> connect 127.0.0.1:10002
[*] Waiting for response......
[*] New node online! Node id is 1

(node 0) >>
```

- `sshtunnel`: Instruct the current node to connect to another child node via ssh tunnel

```
agent-2: ./stowaway_agent -l 10003
```

```
(node 0) >> sshtunnel 127.0.0.1:22 10003
[*] Please choose the auth method(1.username&&password / 2.certificate): 1
[*] Please enter the username: ph4ntom
[*] Please enter the password: ******
[*] Waiting for response.....
[*] New node online! Node id is 2

(node 0) >>
```

In highly restricted environments, Stowaway can use SSH tunneling to disguise its traffic as SSH and bypass firewall restrictions.

- `upload`: Upload file to the current node

```
(node 0) >> upload test.7z test.xxx
[*] File transmitting, please wait...
136.07 KiB / 136.07 KiB [-----------------------------------------------------------------------------------] 100.00% ? p/s 0s
```

- `download`: Download file from the current node

```
(node 0) >> download test.xxx test.xxxx
[*] File transmitting, please wait...
136.07 KiB / 136.07 KiB [-----------------------------------------------------------------------------------] 100.00% ? p/s 0s
```

- `forward`: Map port on the admin to remote port

```
(node 0) >> forward 9000 127.0.0.1:22
[*] Trying to listen on 0.0.0.0:9000......
[*] Waiting for response......
[*] Forward start successfully!
(node 0) >>
```

```
$ ssh 127.0.0.1 -p 9000
Password:
 # ph4ntom @ ph4ntoms-MBP in ~ 👑 [17:19:51]
$
```

- `stopforward`: Close the remote mapping on the admin

```
(node 0) >> stopforward
[0] All
[1] Listening Addr : [::]:9000 , Remote Addr : 127.0.0.1:22 , Active Connections : 1
[*] Do you really want to shut down forward?(yes/no): yes
[*] Please choose one to close: 1
[*] Closing......
[*] Forward service has been closed successfully!
```

- `backward`: Reverse map the port on the current agent to the local port on the admin

```
(node 0) >> backward 9001 22
[*] Trying to ask node to listen on 0.0.0.0:9001......
[*] Waiting for response......
[*] Backward start successfully!
(node 0) >>
```

```
$ ssh 127.0.0.1 -p 9001
Password:
 # ph4ntom @ ph4ntoms-MBP in ~ 🌈 [17:22:14]
$
```

- `stopbackward`: Close the reverse mapping on the current node

```
(node 0) >> stopbackward
[0] All
[1] Remote Port : 9001 , Local Port : 22 , Active Connections : 1
[*] Do you really want to shut down backward?(yes/no): yes
[*] Please choose one to close: 1
[*] Closing......
[*] Backward service has been closed successfully!
```

- `shutdown`: Take the current node offline

```
(node 1) >> shutdown
(node 1) >>
[*] Node 1 is offline!
```

- `back`: Return to main panel

```
(node 1) >> back
(admin) >>
```

- `exit`: Exit Stowaway 

```
(node 1) >> exit
[*] Do you really want to exit stowaway?(y/n): y
[*] BYE!
```

## TODO

- [x] Fix potential bugs
- [x] Support TLS
- [ ] Support multi startnode

### Attention

- This project is just for fun. The design and code structure are not strict, so please do not take it too seriously.
- The admin node MUST be online if you want to add a new node into the network
- The admin only supports one directly connected agent node, but the agent node has no such restriction
- If you run the admin on Windows, download [ansicon](https://github.com/adoxa/ansicon/releases) first (or from [here](https://github.com/ph4ntonn/Stowaway/blob/master/ansicon/ansi189-bin.zip)). Then go to the folder matching your system architecture and run `ansicon.exe -i`; otherwise, garbled characters may appear in the admin console.
- This program only supports the standard `UDP ASSOCIATE` described in [RFC1928](https://www.ietf.org/rfc/rfc1928.txt). Please check the tools you are using (scanners, etc.) and make sure their packet construction complies with RFC1928. Packet loss handling is also your responsibility.

## 404Starlink

<img src="https://github.com/knownsec/404StarLink/raw/master/Images/logo.png" width="30%">

Stowaway has joined [404Starlink](https://github.com/knownsec/404StarLink)

## Acknowledgement

Thanks to the following developers and projects for their help during the development of Stowaway
- [lz520520](https://github.com/lz520520)
- [SignorMercurio](https://github.com/SignorMercurio)
- [MM0x00](https://github.com/MM0x00)
- [r0ck3rt](https://github.com/r0ck3rt)
- [Termite](https://github.com/rootkiter/Termite)
- [Venom](https://github.com/Dliv3/Venom)
