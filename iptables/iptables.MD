`IPTABLES IN CENTOS 7`

# 1. Introduction:
- Iptables is a powerful tool used to manage the Linux firewall. It is used to set up, maintain, and inspect the tables of IP packet filter rules in the Linux kernel. One of the most common use cases of iptables is to block incoming access from specific IP addresses.
```
--append  -A Append to chain
--delete  -D Delete matching rule from chain
--delete  -D Delete rule rulenum (1 = first) from chain	
--insert  -I Insert in chain as rulenum (default 1=first)
--replace -R Replace rule rulenum (1 = first) in chain
--list    -L List the rules in a chain or all chains

--source      -s [!] address[/mask] source specification
--destination -d [!] address[/mask] destination specification                         
--jump        -j target

INPUT = Incoming Access
OUTPUT = Outgoing Access
-I = Insert
-D = Delete
-s = Source Ip Address
-j = Target Action
DROP = Block action
```
- Types of Chains of iptables
```
INPUT: INPUT chains define rules set for packets intended to sockets of localhost.
FORWARD: FORWARD chains used for various packets routed from the device.
OUTPUT: OUTPUT chains used for locally produced packets, specified to be set outside.
PREROUTING: PREROUTING chains are used for changing packets as these packets arrive.
POSTROUTING: POSTROUTING chains are used for changing packets as these packets are leaving.
```

# 2. How to Install Iptables on CentOS 7
- Starting with CentOS 7, FirewallD replaces iptables as the default firewall management tool.
FirewallD is a complete firewall solution that can be controlled with a command-line utility called firewall-cmd. If you are more comfortable with the Iptables command line syntax, then you can disable FirewallD and go back to the classic iptables setup.
- Disable FirewallD:
  - 1. Type the following command to stop the FirewallD service:
    ```
    sudo systemctl stop firewalld
    ```
  - 2. Disable the FirewallD service to start automatically on system boot:
    ```
    sudo systemctl disable firewalld
    ```
  - 3. Mask the FirewallD service to prevent it from being started by another services:
    ``` 
    sudo systemctl mask --now firewalld
    ```
2. Install and Enable Iptables
  - 1. Run the following command to install the iptables-service package from the CentOS repositories:
    ```
    sudo yum install iptables-services
    ```
  - 2. Once the package is installed start the Iptables service:
    ```
    sudo systemctl start iptables
    sudo systemctl start ip6tables
    ```
  - 3. Mask the FirewallD service to prevent it from being started by another services:
    ``` 
    sudo systemctl enable iptables
    sudo systemctl enable ip6tables
    ```
  - 4. Check the iptables service status with:
    ```
    sudo systemctl status iptables
    sudo systemctl status ip6tables
    ```
  - 5. To check the current iptables rules use the following commands:
    ``` 
    sudo iptables -nvL
    sudo ip6tables -nvL
    ```
   - 6. By default only the SSH port 22 is open. The output
    ```
    Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
 5400 6736K ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED

    0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0           
    2   148 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0           
    3   180 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22
    0     0 REJECT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 REJECT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited

Chain OUTPUT (policy ACCEPT 4298 packets, 295K bytes)
 pkts bytes target     prot opt in     out     source               destination     
   ```