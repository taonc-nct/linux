# `IPTABLES IN CENTOS 7`

# `Introduction:`
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
    
   - 6. run *vi /etc/syconfig/iptables* :
    ```
    # sample configuration for iptables service
    # you can edit this manually or use system-config-firewall
    # please do not ask us to add additional ports/services to this default configuration
    *filter
    :INPUT ACCEPT [0:0]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [0:0]
    -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
    -A INPUT -p icmp -j ACCEPT
    -A INPUT -i lo -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
    -A INPUT -j REJECT --reject-with icmp-host-prohibited
    -A FORWARD -j REJECT --reject-with icmp-host-prohibited
    COMMIT
    ```

## `Config iptables`

- Block ssh from one IP:
  ``` 
  -A INPUT -s <ip> -p tcp  -m state --state  NEW -m  tcp --dport 22 -j DROP
  ```
  - -A : APPEND
  - INPUT: prevent from outside to machine
  - s: source address
  - -p, --protocol [!] protocol can be one udp, tcp, icmp
  - dport:  --destination-port 
  - state: This module, when combined with connection tracking, allows access to the connection tracking state for this packet.--state stateWhere state is a comma separated list of the connection states to match. Possible states are INVALID meaning that the packet could not be identified for some reason which includes running out of memory and ICMP errors which don't correspond to any known connection, ESTABLISHED meaning that the packet is associated with a connection which has seen packets in both directions, NEW meaning that the packet has started a new connection, or otherwise associated with a connection which has not seen packets in both directions, and RELATED meaning that the packet is starting a new connection, but is associated with an existing connection, such as an FTP data transfer, or an ICMP error.
  - **note:**
  ```
  NEW: Khi có một gói tin mới được gởi tới và không nằm trong bất kỳ connection nào hiện có, hệ thống sẽ khởi tạo một kết nối mới và gắn nhãn NEW cho kết nối này. Nhãn này dùng cho cả TCP và UDP.

  ESTABLISHED: Trạng thái chuyển NEW to ESTABLISHED khi nhận được phản hồi hợp lệ từ phía đối diện của kết nối. Với kết nối TCP, nó chính là SYN/ACK và với UDP/ICMP, là phản hồi mà ở đó địa chỉ nguồn và địa chỉ đích được hoán đổi.
  
  RELATED: Gói tin được gởi tới không thuộc về một kết nối hiện có nhưng có liên quan đến một kết nối đang có trên hệ thống. Đây có thể là một kết nối phụ hỗ trợ cho kết nối chính, ví dụ như giao thức FTP có kết nối chính dùng để chuyển lệnh và kết nối phụ dùng để truyền dữ liệu.

  INVALID: Gói tin được đánh dấu INVALID khi gói tin này không có bất cứ quan hệ gì với các kết nối đang có sẵn, không thích hợp để khởi tạo một kết nối mới hoặc đơn giản là không thể xác định được gói tin này, không tìm được kết quả trong bảng định tuyến.

  UNTRACKED: Gói tin có thể được gắn hãn UNTRACKED nếu gói tin này đi qua bảng raw và được xác định ( gắn cờ ) là không cần theo dõi gói này trong bảng connection tracking.

  SNAT: Đó là trạng thái sẽ được đánh dấu khi gói tin được chỉnh sửa phần source address bởi quá trình NAT. Nó được dùng bởi hệ thống Connection tracking để thay đổi lại source address ở gói tin phản hồi lại.

  DNAT: Đó là trạng thái sẽ được đánh dấu khi gói tin được chỉnh sửa phần destination address bởi quá trình NAT. Nó được dùng bởi hệ thống Connection tracking để thay đổi lại destination address ở gói tin phản hồi lại.
  ```

- Iptables setting multiple multiports in one rule:
  ```
  -A INPUT -s <IP> -p tcp -m state --state NEW -m tcp -m multiport --dports 22,23,24 -j ACCEPT
  ```
