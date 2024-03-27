# Enhancing Web Server Security with SELinux: A Practical Guide
### What is SELinux?
SELinux stands for Security-Enhanced Linux which is a security architecture for Linux systems that allows administrators to have more control over users that are accessible to the system.
A set of rules are defined and used to tell the SELinux what can or cannot be accessed. Whenever a request is made to access an object, SELinux will check with an AVC where permissions are cached for subjects and objects.
So, this project will briefly demonstrate how SELinux works.
## Content Page
## Objective
- Understand how SELinux contexts work and their impact on access control
- Troubleshooting SELinux denials and ensuring proper permissions for web server content.
- Implement secure administrative practices through sudo transitions and user role management.
## Running SELinux
This project is designed to be run on **Fedora 37**. To smoothly complete this project, make sure you are running on the right version though Red Hat Enterprise Linux and its derivative work well too.
Execute `$setstatus` to test if SELinux is capable of running in enforcing mode. You should get output like this:
```
SELinux status: enabled  
SELinuxfs mount: /sys/fs/selinux  
SELinux root directory: /etc/selinux  
Loaded policy name: targeted  
Current mode: enforcing  
Mode from config file: enforcing  
Policy MLS status: enabled
```

## Changing httpd port mandatory policy
1. Install apache httpd server.
  <p align="center">
   <img width="501" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/499504ac-ad41-4c42-ad96-2d6708e97b65">
    
   </p>

3.  Make sure that your httpd server is running and accepting connections from your host machine, and also the SElinux user and context type that it is using.
   <p align="center">
     <img width="651" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/9bc7be80-82ca-487e-85db-6c126891a8d8">

   </p>

3. Update firewall rules to allow connections to your web server by execute `$systemctl status firewalld`.
4. Change the default port (TCP80) to a custom port (TCP10xxx) in the /etc/httpd/conf/httpd.conf file, using the last 3 digits of any numbers you like.(Remember it!)
5. Update the SELinux targeted policy to include the custom port (10xxx) as a valid http_port_t.
6. Verify the server functionality by `curl` to ensure your server is working just fine in port 10xxx. Don't forget to reconfigure firewalld to permit connections.
   


## Enabling HTTPS and certificate files SElinux context
## SElinux context for web server CONTENT.
## Create a sudo transition for a cy5130_u to become webadm_r
## Create a content page for the user
