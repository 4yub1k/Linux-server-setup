# Linux-server-setup
**Updating**\
Any linux operating system these steps will be same, only change the commands for them.
ignore : spelling mistakes

### Debian install:
1- Select install\
2- Fill all options\
3- Select guided partition > selection \var, \swap, \, \home.

![image](https://user-images.githubusercontent.com/45902447/150688302-ac5cd69c-7ba3-4085-a2b7-e15af3b22a9d.png)
![image](https://user-images.githubusercontent.com/45902447/150688325-23538314-1b35-4bd4-b43e-52d5ea4af2cf.png)

4- Select country settings for apt

5- During software install only SSH.

![2022-01-23 23_08_59-Window](https://user-images.githubusercontent.com/45902447/150731647-3ddb382d-85fe-463e-ab3e-bdf554dab129.png)

6- Login to os: <as root>
```
apt update && apt upgrade
apt-get install sudo, or apt install sudo
```

install ufw firewall (easy to use then iptables)
```
apt install ufw

ufw allow 22, or ufw allow ssh
```
**DO NOT ENABLE IT IF YOU HAVE NO PHYSICAL/ANY OTHER ACCESS TO SERVER, OR YOU HAVEN'T ALLOWED SSH, I RECOMMAND TO ENABLE IT AFTER CONFIGURING SSH**
```
systemctl enable ufw
systemctl restart ufw
ufw status
```
![image](https://user-images.githubusercontent.com/45902447/150737459-1b61da63-7e24-4ba8-9a6e-b1dd9392ef41.png)

7- Configure ssh:
  
First create Backup :) 
  ```
  cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
  ```
  ```
  nano /etc/ssh/sshd_config
  ```
  Remove # from these lines.They should look like this
  ```
  #PasswordAuthentication yes
  #PermitRootLogin yes
  ```
  ![image](https://user-images.githubusercontent.com/45902447/150732394-040d1c85-b576-4736-8b74-5aa45c1394bb.png)

  ![image](https://user-images.githubusercontent.com/45902447/150732973-fe43df44-f758-4a8a-a1d5-4e7daef34bc9.png)
  
  To save : crtl + x , y, enter
  ```
  systemctl restart ssh, systemctl restart sshd
  ```
  The main difference is that sshd is a server (like a web server serving https) and SSH is a client (think of a web browser)

  get the ip of server:
  ```
  ifconfig
  ```
  ![image](https://user-images.githubusercontent.com/45902447/150734914-c367cf51-b978-4d12-8cc3-87046e1b35f1.png)

  Now you can connect to server from you computer usig ssh

 ### Now, Lets configure it from our computer (ssh)
  ```
  ssh root@192.168.242.131
  Enter yes if first time connecting,
  else Enter the root password
  ```
  ```
  apt update && apt upgrade
  hostname
  ```
  ![image](https://user-images.githubusercontent.com/45902447/150733878-9eb28976-88f2-4593-b331-75643b67f136.png)

  If you want to change hostname
  these commands are different for for different operating systems.
  ```
  hostnamectl set-hostname <name>
  ```
  i've changed it to the "test-server"
  
  ![image](https://user-images.githubusercontent.com/45902447/150734342-76b198a9-cac2-4adc-ba22-1bd0b4c6175b.png)

Set hostname in hostfile:
  ```  
  nano /etc/hosts
  ```
   ```
  127.0.0.1       localhost
  192.168.242.131 test-server

  # The following lines are desirable for IPv6 capable hosts
  ::1     localhost ip6-localhost ip6-loopback
  ff02::1 ip6-allnodes
  ff02::2 ip6-allrouters
  ```
![image](https://user-images.githubusercontent.com/45902447/150735298-41153c76-25ea-4821-a93a-cf5b49dc75fc.png)

Now add limited user with limited previliages\
if you have created the user during installation then skip this.
  ```
  adduser <username>
  ```
set password\
set user name\
salah is user here!
  
![image](https://user-images.githubusercontent.com/45902447/150736800-f555ce97-4a06-40a2-bcb0-7bdf73f5417e.png)
  
![image](https://user-images.githubusercontent.com/45902447/150737021-d1dc90e1-bedf-43da-bb4f-589ede01bca4.png)

Now to make it run root commands will will add it to sudo group
  
![image](https://user-images.githubusercontent.com/45902447/150737101-b5fe817e-1a7a-436e-81a0-3a8e9a1be52a.png)
  
Now exit the server and log in using the user "salah" we added
  
![image](https://user-images.githubusercontent.com/45902447/150738158-dbb027a4-4064-4d41-a2ec-acb6441fcc80.png)

 We can see we logged in as "salah" and hostname "test-server"  --> user@hostname
  
![image](https://user-images.githubusercontent.com/45902447/150738266-f86799b2-ac75-4194-8bdd-d1b6bd1813ae.png)

use clear to clear the screen

## SSH key based Authentication:
open you command line WINDOWS,
  ```
  ssh-keygen -t rsa -b 4096
  hit enter for file location
  Enter password, if you think someone can access to user keys on computer, it will added another layer of security
  or you can leave them blank
  ```
  ![image](https://user-images.githubusercontent.com/45902447/150739715-042cfa2a-857b-4457-abda-76b35f1a3ad7.png)

Inside windows, . we can see the rsa keys are created.
  
  ![image](https://user-images.githubusercontent.com/45902447/150739996-590bbc3c-ff95-4a1b-8026-6d90df4cdbd3.png)

In ssh window of server\
  Make a .ssh directory
  ```
  mkdir -p ~/.ssh
  ```
  makes a .ssh directory in home (~)
  
 ![image](https://user-images.githubusercontent.com/45902447/150741203-71a9b152-ba6b-450d-9497-e5f75399ac62.png)
  
  now cd to this directory or use location,
  ```
  scp "/Users/Salah Ud Din/.ssh/id_rsa.pub" salah@192.168.242.131:~/.ssh/salah.pub
  ```
  or you can also use this command it will directly create the authorie_keys
  ```
  scp "/Users/Salah Ud Din/.ssh/id_rsa.pub" salah@192.168.242.131:~/.ssh/authorized_keys
  ```
  ![image](https://user-images.githubusercontent.com/45902447/150745445-d36a20bb-a3d2-499c-a351-698bc6f68c25.png)

  ![image](https://user-images.githubusercontent.com/45902447/150745743-95111f84-1417-4d94-a3d9-34d534a67aed.png)

  lets copy it to authorized keys.\
  salah.pub is the file we uploaded
  ```
  cat ~/.ssh/salah.pub >> ~/.ssh/authorized_keys
  ```
  
  you can remove salah.pub if you want as we have copied it to authorized_keys
  ```
  rm ~/.ssh/salah.pub
  ```
  
  Set permissions:\
  chmod 700 for .ssh
  ```
  salah@test-server:~$ chmod 700 ~/.ssh/
  ```
  chmod 600 for all files inside .ssh
  ```
  salah@test-server:~$ chmod 600 ~/.ssh/*
  ```
  ![image](https://user-images.githubusercontent.com/45902447/150746409-7323e07c-3fd6-4314-8c54-2801b9c99fec.png)
  
  
  Now try to login to server;
  ```
  ssh salah@192.168.242.131
  ```
  ![image](https://user-images.githubusercontent.com/45902447/150746810-4c1dccc7-a215-41f4-ad1b-9daf12ddbeaf.png)
  
  we logged in succesfully.
  
  
## Let's disable root login and login with password.
  Create Backup again :) 
  ```
  sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
  ```
  
  set PasswordAuthentication and root login to no
  ```
  PermitRootLogin no
  PasswordAuthentication no  
  ```
  ```
  sudo nano /etc/ssh/sshd_config
  ```
  ![image](https://user-images.githubusercontent.com/45902447/150748324-7592d1c7-a099-4de1-86f9-584f41bc27bd.png)

  ![image](https://user-images.githubusercontent.com/45902447/150748713-504fb7e0-baae-48be-944a-f0422fddbbc7.png)

  save the file.
  ```
  systemctl restart ssh, or sudo service ssh restart
  ```
  
  Done ssh setup done !
