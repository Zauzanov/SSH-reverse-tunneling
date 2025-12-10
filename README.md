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
