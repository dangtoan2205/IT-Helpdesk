How to Install a Private OpenVPN Server on Ubuntu 22.04 [Step-by-Step]
---------

<a href="https://www.cherryservers.com/blog/install-openvpn-server-ubuntu">Link tham khảo</a>

OpenVPN cung cấp khả năng thiết lập các kênh bảo mật qua một cổng UDP hoặc TCP duy nhất, sử dụng phương pháp mã hóa, xác thực và chứng chỉ của OpenSSL. 

- Máy chủ OpenVPN riêng là gì?

  - Hãy bắt đầu với việc hiểu về máy chủ OpenVPN riêng. Chúng ta có thể định nghĩa nó là một máy chủ VPN chạy trên OpenVPN, phần mềm mã nguồn mở. Tuy nhiên, nó là riêng tư, có nghĩa là nó được thiết lập bởi các tổ chức để sử dụng riêng của họ. Ngay cả cá nhân cũng có thể thiết lập máy chủ OpenVPN cho mục đích sử dụng cá nhân.

  - Điều quan trọng về các máy chủ OpenVPN riêng là các biện pháp kiểm soát nghiêm ngặt được áp dụng để truy cập vào chúng. Chỉ những người dùng đã được cấp phép và có thông tin xác thực phù hợp mới có thể sử dụng mạng.

  - Một điểm tuyệt vời khác về các máy chủ OpenVPN riêng là bạn có thể tùy chỉnh chúng một cách đáng kể. Bạn có thể điều chỉnh chúng theo các tính năng bảo mật, cấu hình mạng và quyền truy cập dựa trên yêu cầu của bạn.

- Yêu cầu trước khi cài đặt

  - Hãy đảm bảo rằng hệ thống Ubuntu của bạn đáp ứng các yêu cầu cần thiết trước khi tiếp tục cấu hình máy chủ OpenVPN.

  - Hệ thống Ubuntu 22.04: Đảm bảo rằng hệ thống Ubuntu 22.04 của bạn đang hoạt động và có thể truy cập được. Cần có quyền quản trị hoặc quyền root.

  - Truy cập Terminal: Bạn có thể kết nối trực tiếp hoặc sử dụng SSH để truy cập từ xa.

  - Kiến thức cơ bản về dòng lệnh: Cần có sự quen thuộc với các thao tác trên dòng lệnh để có thể theo dõi các hướng dẫn trong bài viết này.

  - Hiểu biết về các khái niệm mạng: Điều này sẽ giúp bạn cấu hình máy chủ một cách chính xác. Bạn cần hiểu rõ về địa chỉ IP, các mạng con (subnet), và tường lửa (firewall).

Khi đã đáp ứng đủ các yêu cầu trên, bạn có thể tiếp tục theo các hướng dẫn được cung cấp trong bài viết này.

## Step 1: System update
```
sudo apt update
```

## Step 2: Install OpenVPN and Easy-RSA

OpenVPN là phần mềm hàng đầu để thiết lập và quản lý các kết nối VPN. Trong khi đó, Easy-RSA là một tiện ích được thiết kế để đơn giản hóa việc tạo ra các chứng chỉ SSL và khóa cần thiết cho việc giao tiếp an toàn.

Bạn có thể cài đặt hai phần mềm này bằng cách sử dụng các lệnh dưới đây.

Install the OpenVPN:
---

```
sudo apt install openvpn
```

![image](https://github.com/user-attachments/assets/e66521b5-bed7-450f-a184-2c9a89c83ef4)


Install the Easy-RSA:
---

```
sudo apt install easy-rsa
```

![image](https://github.com/user-attachments/assets/c2dc6d22-2c61-4eff-8fb3-2cac25ac4a79)

## Step 3: Initialize the Easy-RSA PKI

Khi Easy-RSA đã được cài đặt, bạn cần phải khởi tạo Cơ sở hạ tầng khóa công khai (PKI). Bước này là cần thiết để thiết lập cơ quan cấp chứng chỉ và tạo các chứng chỉ và khóa cần thiết cho OpenVPN.

Ban đầu, hãy sao chép thư mục cấu hình Easy-RSA vào một vị trí khác. Biện pháp này sẽ bảo vệ các thay đổi của bạn khỏi bị ghi đè bởi các lần nâng cấp gói phần mềm trong tương lai.

```
sudo cp -r /usr/share/easy-rsa /etc/
```

Điều hướng đến thư mục đã sao chép và khởi tạo Cơ sở hạ tầng khóa công khai (PKI). ( Navigate to the copied directory and initialize the Public Key Infrastructure.)

```
cd  /etc/easy-rsa/
```

```
sudo ./easyrsa init-pki
```

![image](https://github.com/user-attachments/assets/c9fc446a-17b6-4151-8ba5-763b15fb8d51)

## Step 4: Generate the certificate authority

Trong bước này, bạn sẽ tạo chứng chỉ và khóa của cơ quan cấp chứng chỉ (Certificate Authority - CA). ( In this step, you will generate the Certificate Authority certificate and key.)

```
sudo ./easyrsa build-ca
```

![image](https://github.com/user-attachments/assets/252b3e45-b117-4bba-a611-03c767a482b2)

Ở đây, bạn sẽ được yêu cầu nhập mật khẩu và tên chung cho khóa của Cơ quan cấp chứng chỉ (CA) để bảo mật. Khóa CA cần được giữ an toàn vì nó sẽ ký các chứng chỉ cho VPN của bạn.( Here you'll be prompted to enter a password and a common name for the Certificate Authority key for security. The CA key should be kept secure as it signs certificates for your VPN.)

## Step 5: Generate Diffie-Hellman parameters

Với khóa Diffie-Hellman, bạn có thể thiết lập các kết nối an toàn với mã hóa mạnh. Tuy nhiên, bạn có thể bỏ qua bước này hoàn toàn, nhưng điều đó sẽ khiến cấu hình VPN của bạn kém an toàn hơn.( With Diffie-Hellman keys, you can establish secure connections with strong encryption. However, It is possible to skip this step entirely, but you would leave your VPN setup less secure.)

```
sudo ./easyrsa gen-dh
```

![image](https://github.com/user-attachments/assets/d8ff59ce-0e5a-4ae0-bafb-07130652c360)

## Step 6: Generate OpenVPN server certificate and key

Trong bước này, bạn tạo chứng chỉ và khóa máy chủ cho máy chủ OpenVPN của mình. Ở đây, bạn cần thay đổi thư mục làm việc hiện tại sang thư mục Easy-RSA.( In this step, you generate the server certificate and key for your OpenVPN server. Here, you first have to change the current working directory to the Easy-RSA directory.)

```
cd  /etc/easy-rsa
```

Bạn có thể thay thế "server" bằng một tên máy chủ khác trong đoạn mã được cung cấp. Tên này sẽ được sử dụng cho các kết nối của client để nhận diện máy chủ.( Feel free to exchange "server" with a different server name in the provided code. This name will be used for client connections to identify the server.)

```
sudo ./easyrsa build-server-full server nopass
```

![image](https://github.com/user-attachments/assets/ce95b6e7-f767-418a-82c4-e73a73c8419a)

The `nopass` flag disables passphrase protection.( The `nopass` flag disables passphrase protection.)

## Step 7: Generate HMAC key

Khóa xác thực TLS/SSL được chia sẻ trước thêm một lớp bảo mật bổ sung bằng cách bao gồm chữ ký HMAC trong mỗi gói bắt tay SSL/TLS. Biện pháp này giúp ngăn chặn các cuộc tấn công DoS tiềm ẩn và tràn cổng UDP. Mặc dù không bắt buộc, tôi khuyến khích bạn nên xem xét bước này, đặc biệt nếu bạn có ý định bảo vệ chống lại các cuộc tấn công cụ thể.</br>

( The TLS/SSL pre-shared authentication key adds an extra layer of security by including an HMAC signature on every SSL/TLS handshake packet. This measure helps prevent potential DoS attacks and UDP port flooding. Although not mandatory, I strongly advise you to consider this step, particularly if you intend to safeguard against specific attacks.)

```
sudo openvpn --genkey secret /etc/easy-rsa/pki/ta.key
```

## Step 8: Generate OpenVPN revocation certificate

Tạo một chứng chỉ thu hồi để vô hiệu hóa các chứng chỉ đã được ký trước đó.( Generate a revocation certificate to invalidate previously signed certificates.)

```
sudo ./easyrsa gen-crl
```

![image](https://github.com/user-attachments/assets/2ac1058b-bf01-4e55-9526-b7d8ab91e194)

Step 9: Copy server certificates and keys

Trong bước này, sao chép các chứng chỉ và khóa máy chủ đã tạo vào thư mục cấu hình của máy chủ OpenVPN. Những tệp này là cần thiết để thiết lập các kết nối an toàn giữa máy chủ và các client.

( In this step, copy the generated server certificates and keys to the OpenVPN server's configuration directory. These files are necessary for establishing secure connections between the server and clients.)

```
sudo cp -rp /etc/easy-rsa/pki/{ca.crt,dh.pem,ta.key,crl.pem,issued,private} /etc/openvpn/server/
```

![image](https://github.com/user-attachments/assets/f752c921-719d-4e7c-b031-f89cb5af4978)

## Step 10: Generate OpenVPN client certificates and keys

This step involves creating certificates and keys for each client that will connect to the OpenVPN server. These certificates and keys are unique to each client and are used for authentication. Use the client's actual name instead of "clientname" in the code below.

( This step involves creating certificates and keys for each client that will connect to the OpenVPN server. These certificates and keys are unique to each client and are used for authentication. Use the client's actual name instead of "clientname" in the code below.)

```
cd /etc/easy-rsa
```

```
sudo ./easyrsa build-client-full clientname nopass
```

![image](https://github.com/user-attachments/assets/7772f35c-749f-493d-ae23-95d9f95eb2da)

Step 11: Create client directories and copy files

Create directories for each client and copy their certificates and keys. Change "clientname" to the real name of the client you are configuring. Every client must have their own directory. Organizing client-specific directories ensures that each client has a designated place to store their credentials. This separation increases security and simplifies management.

( Create directories for each client and copy their certificates and keys. Change "clientname" to the real name of the client you are configuring. Every client must have their own directory. Organizing client-specific directories ensures that each client has a designated place to store their credentials. This separation increases security and simplifies management.)

```
sudo mkdir /etc/openvpn/client/clientname
```

Sau đó, các tệp liên quan đến client cụ thể phải được sao chép vào thư mục của client.

(After that, specific client_related files must be copied to your client's directory.)

```
sudo cp -rp /etc/easy-rsa/pki/{ca.crt,issued/clientname.crt,private/clientname.key} /etc/openvpn/client/clientname/
```

## Step 12: Configure OpenVPN server

Here, you're copying a sample server configuration file to the OpenVPN server configuration directory. This file contains settings that control how the OpenVPN server operates.

( Here, you're copying a sample server configuration file to the OpenVPN server configuration directory. This file contains settings that control how the OpenVPN server operates.)

```
sudo cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf /etc/openvpn/server/
```

## Step 13: Edit the server configuration file

Để chỉnh sửa cấu hình máy chủ, bạn sẽ cần sử dụng một trình soạn thảo văn bản như nano. Tệp cấu hình đi kèm cung cấp một số tham số có thể điều chỉnh để phù hợp với sở thích của bạn. Những tham số này điều khiển các khía cạnh khác nhau, chẳng hạn như cổng lắng nghe của máy chủ, loại thiết bị tunnel, sở thích mã hóa và nhiều thứ khác.

( To modify the server configuration, you'll need to utilize a text editor such as nano. The included configuration file offers several adjustable parameters that cater to your preferences. These parameters oversee various aspects, like the server's listening port, tunnel device type, encryption preferences, and more.)

```
sudo nano /etc/openvpn/server/server.conf
```


![image](https://github.com/user-attachments/assets/705f5c13-9bb2-4341-b3df-5c4e60b42acd)

-  **Modify the file based on your preferences. Refer to the explanations below:**

-  **port 1194:** The port on which OpenVPN will listen for incoming connections. (Cổng mà OpenVPN sẽ lắng nghe các kết nối đến)

-  **proto udp4:** Specifies the use of UDP as the transport protocol for communication. (Chỉ định sử dụng UDP làm giao thức truyền tải cho giao tiếp.)

-  **dev tun:** Defines the type of tunnel network device to be used. (Xác định loại thiết bị mạng tunnel sẽ được sử dụng.)

-  **ca ca.crt:** Specifies the Certificate Authority certificate file to verify the authenticity of remote peers. (Chỉ định tệp chứng chỉ của Cơ quan cấp chứng chỉ (CA) để xác minh tính hợp lệ của các đối tác từ xa.)

-  **cert issued/server.crt:** The server's certificate file is used for authentication. (Tệp chứng chỉ của máy chủ được sử dụng để xác thực.)

-  **key private/server.key:** The server's private key file, which must be kept confidential. (Tệp khóa riêng của máy chủ, phải được giữ bí mật.)

-  **dh dh.pem:** The Diffie-Hellman parameters file used for key exchange. (Tệp tham số Diffie-Hellman được sử dụng cho việc trao đổi khóa.)

-  **topology subnet:** Sets the topology to the subnet, allowing OpenVPN to route traffic between clients. (Cài đặt topology là subnet, cho phép OpenVPN định tuyến lưu lượng giữa các client.)

-  **server 172.16.20.0 255.255.255.0:** Defines the server's virtual IP address pool and subnet mask. (Định nghĩa phạm vi địa chỉ IP ảo của máy chủ và mặt nạ mạng con.)

-  **ifconfig-pool-persist /var/log/openvpn/ipp.txt:** Stores persistent IP assignments in the specified file. (Lưu trữ các phân phối IP bền vững trong tệp được chỉ định.)

-  **push "redirect-gateway def1 bypass-dhcp":** Redirects the client's default gateway through the VPN tunnel. (Chuyển hướng gateway mặc định của client qua tunnel VPN.)

-  **push "dhcp-option DNS 208.67.222.222" and push "dhcp-option DNS 208.67.220.220":** Configures DNS servers for clients. (Cấu hình các máy chủ DNS cho client.)

-  **client-to-client:** Allows communication between clients connected to the same OpenVPN server. (Cho phép giao tiếp giữa các client kết nối với cùng một máy chủ OpenVPN.)

-  **keepalive 10 120:** Sets a 10-second interval for ping/keepalive messages. (Cài đặt khoảng thời gian 10 giây cho các tin nhắn ping/keepalive.)

-  **tls-auth ta.key 0:** Utilizes a secret TLS authentication key for added security. (Sử dụng khóa xác thực TLS bí mật để tăng cường bảo mật.)

-  **cipher AES-256-CBC:** Specifies the encryption cipher for data transmission. (Xác định thuật toán mã hóa cho việc truyền tải dữ liệu.)

-  **persist-key and persist-tun:** Ensures that OpenVPN's encryption keys and tunnel network device settings persist across restarts. (Đảm bảo rằng các khóa mã hóa của OpenVPN và cài đặt thiết bị mạng tunnel sẽ tồn tại sau các lần khởi động lại.)

-  **status /var/log/openvpn/openvpn-status.log:** Specifies the file to store runtime status information. (Chỉ định tệp để lưu trữ thông tin trạng thái trong thời gian chạy.)

-  **log-append /var/log/openvpn/openvpn.log:** Appends log output to the specified log file. (Ghi thêm đầu ra log vào tệp log được chỉ định.)

-  **verb 3:** Sets the verbosity level of OpenVPN's logging. (Cài đặt mức độ chi tiết của log OpenVPN.)

-  **explicit-exit-notify 1:** Informs clients to explicitly send an exit notification when disconnecting. (Thông báo cho client gửi thông báo thoát khi ngắt kết nối.)

-  **auth SHA512:** Specifies the message digest algorithm for data integrity. (Chỉ định thuật toán băm thông điệp cho tính toàn vẹn dữ liệu.)

![image](https://github.com/user-attachments/assets/77a29da4-d8d4-458c-863c-77329ff6c372)

Cấu hình này thiết lập một máy chủ OpenVPN sử dụng UDP trên cổng 1194 với các biện pháp bảo mật khác nhau, cài đặt mạng và tùy chọn ghi log. Nó được cấu hình để định tuyến toàn bộ lưu lượng của client qua VPN và sử dụng mã hóa để giao tiếp an toàn.( This configuration sets up an OpenVPN server using UDP on port 1194 with various security measures, network settings, and logging options. It's configured to route all client traffic through the VPN and uses encryption for secure communication.)

## Step 14: Enable IP forwarding and configure the firewall

Kích hoạt việc chuyển tiếp IP trong cấu hình sysctl. Bước này cho phép máy chủ định tuyến lưu lượng giữa các giao diện của nó. Đây là bước quan trọng để đảm bảo hoạt động chính xác của VPN.( Enable IP forwarding in the sysctl configuration. This step allows the server to route traffic between its interfaces. It's an important step for proper VPN operation.)

```
sudo sed -i 's/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/' /etc/sysctl.conf
```

Apply the changes without rebooting the server.

```
sudo sysctl --system
```

![image](https://github.com/user-attachments/assets/8cd8e3be-9996-4072-bf5a-3d153276aba4)

You should also configure the firewall to allow traffic on UDP port 1194, which is the default port used by OpenVPN.

```
sudo ufw allow 1194/udp
```

![image](https://github.com/user-attachments/assets/5548e4d5-1752-47de-a4c1-a522fa5b2723)

If desired, you can restrict connections to specific sources.

## Step 15: Configure IP masquerading

IP masquerading is a technique used to route traffic from one network to another. In this step, you're updating the Uncomplicated Firewall rules and policies to ensure that masquerading works properly.

First, you need to identify your default network interface. The below command is used to determine the default network interface. It helps identify the network interface through which traffic will be routed.

```
ip route get 8.8.8.8
```

![image](https://github.com/user-attachments/assets/e1fed488-c66a-435c-82b2-407d9c60c65e)

Next, we need to update UFW rules. UFW is a user-friendly interface for managing iptables, Ubuntu's default firewall management tool. The command below opens the UFW configuration file for editing.

```
sudo nano /etc/ufw/before.rules
```

Afterward, we need to update the UFW default forwarding policy. This command uses the 'sed' utility to find and replace a line in the UFW configuration. It changes the default forwarding policy from drop to accept.

```
sudo sed -i 's/DEFAULT_FORWARD_POLICY="DROP"/DEFAULT_FORWARD_POLICY="ACCEPT"/' /etc/default/ufw
```

After making changes to the firewall rules, you need to reload UFW for the changes to take effect. This ensures that the updated rules are applied without requiring a server restart.

```
sudo ufw reload
```

If you encounter any errors, try the command `sudo sed -ie 's/ENABLED=no/ENABLED=yes/' /etc/ufw/ufw.conf` and then type the reload command.

## Step 16: Start the OpenVPN server

Enable and start the OpenVPN server service.

```
sudo systemctl enable --now openvpn-server@server
```

Next, check the service status. The command below will provide information on its current state and recent log messages.

```
sudo systemctl status openvpn-server@server
```

![image](https://github.com/user-attachments/assets/8a7aaa34-3448-4db5-adfb-fdf3373086b2)


Congratulations! Your OpenVPN server is now set up on Ubuntu 22.04. Remember to configure your clients according to your setup.


