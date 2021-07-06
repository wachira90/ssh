# ssh
ssh tip

## ssh_utils_local

### About
SSH local-port-forwarding. You want to be able to access a service or website that your firewall is preventing you from accessing but you know it is available from the remote server.

### Commands
* Definition
```
ssh -fNL LOCAL-IP:LOCAL-PORT:REMOTE-IP:REMOTE-PORT username@remote-host
ssh -fNL LOCAL-IP:LOCAL-PORT:REMOTE-URL:REMOTE-PORT username@remote-host
```
* Examples
```bash
# Example 1
# If you want to access a service that is running on the remote server port 8888,
# but there is a firewall that is preventing you from accessing the port 8888 from the browser.
# For example you would like to do this from the browser but cannot, remote-host:8888
# After running the following command you can access the service from your local browser, localhost:8080
$ ssh -NL 0.0.0.0:8080:localhost:8888 username@remote-host


# Example 2
# If you are not able to access google.com from your local system but the remote server is able to access it.
# After running the following command you can access the google.com from your local browser, localhost:8080
$ ssh -NL 0.0.0.0:8080:google.com:80 username@remote-host
```

* Option Combinations
  * `-L`: Local port forward and open a remote shell.
  * `-NL`: Local port forward but do not open a remote shell.
  * `-fNL`: Local port forward, do not open remote shell, and send this into the background.



## ssh_utils_remote

### About
One use case for using remote port-forwarding is if a local machine is stuck behind a vpn or firewall and it needs to be accessed by a remote machine. Simple ssh will suffice but we use autossh to keep the connection reliably open for a long period of time.

###  Commands
### Allow SSH into first machine (local) from second (remote) and third (third party) machine
Open up ssh port on the first machine, such that it can be accessed by the second machine. We will use the first, second
and third to mean the same machines in all examples.

* The following command should be run on the first machine.
* Autossh requires two additional echo ports on the second machine. Autossh uses these two ports to check if the connection is alive or not. So in all, the second machine must open three ports in the firewall for autossh to work. For this example we use the following ports:
  * ACCESS_PORT = 8080
  * ECHO_PORT_1 = 20000
  * ECHO_PORT_2 = ECHO_PORT_1 + 1 (20001) (This is done automatically by autossh if not manually set).
* The command that needs to be run on the first machine is of the following form:
  ```bash
  # Usage
  $ autossh -M ECHO_PORT_1 -fNR IP-ON-SECOND:ACCESS_PORT:IP-ON-FIRST:SSH-PORT second-user@second-host
  
  # Example
  $ autossh -M 20000 -fNR 0.0.0.0:8080:localhost:22 second-user@second-host
  ```
* The command that needs to be run on the second machine to access the first machine via ssh is of the following form:
  ```bash
  # Usage
  $ ssh first-user@second-host -p ACCESS_PORT
  
  # Example
  $ ssh first-user@locahost -p 8080
  ### OR 
  $ ssh first-user@0.0.0.0 -p 8080
  ```
* The command that needs to be run by a third machine trying to gain access to the first machine via the second machine is of the following form:
  ```bash
  # Usage
  $ ssh first-user@second-host -p ACCESS_PORT
  
  # Example
  $ ssh first-user@second-host -p 8080
  ```
