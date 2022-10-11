
# Create a new user with root access in linux(ubuntu) VPS

Initial Login and create new user and give root permission.


## Process to follow

1. Initial login to the server with root user: 
```bash
ssh root@server_ip
```
2. Create a non root user: 
```bash
adduser nayeem
```
3. Give root access to new user:

```bash
usermod -aG sudo nayeem
```

4. allow openssh in ufw firewall:

```bash
ufw allow OpenSSH
```
5. enable ufw firewall:

```bash
ufw enable
```

6. Login to `nayeem` user :

```bash
ssh nayeem@server_ip
```
7. Change into root user:

```bash
sudo su
```
8. Exit to regular user:

```bash
exit
```
9. Type exit again to go back to original machine
## Acknowledgements

 - [Code with harry youtube](https://youtu.be/MdF6H0WAhSk)
 - [Code with harry blog](https://www.codewithharry.com/blogpost/setup-ubuntu-20-04-server/)
 

