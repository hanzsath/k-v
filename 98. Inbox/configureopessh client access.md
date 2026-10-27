```
labex:project/ $ ssh -V
OpenSSH_8.9p1 Ubuntu-3ubuntu0.10, OpenSSL 3.0.2 15 Mar 2022
labex:project/ $ ssh -V 2>&1
OpenSSH_8.9p1 Ubuntu-3ubuntu0.10, OpenSSL 3.0.2 15 Mar 2022
labex:project/ $ ssh-keygen -t ed25519 -N "" -C "lfcs-client-key" -f ~/.ssh/lfcs_client_ed25519
Generating public/private ed25519 key pair.
Your identification has been saved in /home/labex/.ssh/lfcs_client_ed25519
Your public key has been saved in /home/labex/.ssh/lfcs_client_ed25519.pub
The key fingerprint is:
SHA256:t8o3M98JMWo2Wof+2ptsuIhekqM4AYPZdOJBqfhO5Nk lfcs-client-key
The key's randomart image is:
+--[ED25519 256]--+
| ...             |
|  = .            |
|o* +             |
|B +              |
| * o    S . o    |
|  * E  . . + o   |
| o .  + . O.o    |
|  o. . * O*=.+ . |
|  ....o =.+XB.o  |
+----[SHA256]-----+
labex:project/ $ ssh-keygen -lf ~/.ssh/lfcs_client_ed25519.pub
256 SHA256:t8o3M98JMWo2Wof+2ptsuIhekqM4AYPZdOJBqfhO5Nk lfcs-client-key (ED25519)
labex:project/ $ chmod 600 ~/.ssh/lfcs_client_ed25519
labex:project/ $ chmod 644 ~/.ssh/lfcs_client_ed25519.pub
labex:project/ $ stat -c '%a %n' ~/.ssh ~/.ssh/lfcs_client_ed25519 ~/.ssh/lfcs_client_ed25519.pub
700 /home/labex/.ssh
600 /home/labex/.ssh/lfcs_client_ed25519
644 /home/labex/.ssh/lfcs_client_ed25519.pub
labex:project/ $ cat > ~/.ssh/config <<'EOF'
Host lfcs-target
    HostName 192.0.2.20
    User admin
    Port 2222
    IdentityFile ~/.ssh/lfcs_client_ed25519
    IdentitiesOnly yes
    ServerAliveInterval 30
EOF
labex:project/ $ chmod 600 ~/.ssh/config
labex:project/ $ cat ~/.ssh/config
Host lfcs-target
    HostName 192.0.2.20
    User admin
    Port 2222
    IdentityFile ~/.ssh/lfcs_client_ed25519
    IdentitiesOnly yes
    ServerAliveInterval 30
labex:project/ $ ssh -F ~/.ssh/config -G lfcs-target | grep -E '^(hostname|user|port|identityfile|identitiesonly|serveraliveinterval) '
user admin
hostname 192.0.2.20
port 2222
identitiesonly yes
serveraliveinterval 30
identityfile ~/.ssh/lfcs_client_ed25519
```