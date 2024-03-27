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

3. Update firewall rules to allow connections to your web server by executing `$systemctl status firewalld`.
  <P align="center">
    <img width="503" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/1d7e24ab-d031-424e-8e44-94ef3df222ca">

  </P>
4. Change the default port (TCP80) to a custom port (TCP10xxx) in the /etc/httpd/conf/httpd.conf file, using the last 3 digits of any numbers you can remember.
<p align="center">
  <img width="485" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/ab321049-6462-43fe-bfc1-2d0a2448b258">

</p>
5. Update the SELinux targeted policy to include the custom port (10xxx) as a valid http_port_t.
  <p align="center">
    <img width="423" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/bb3b09c9-e741-40a5-aab7-fde503efdf5e">

  </p>
6. Verify the server functionality by `curl` to ensure your server is working just fine in port 10xxx. Don't forget to reconfigure firewalld to permit connections.
   <P align="center">
     <img width="487" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/bc0198ec-f6e8-433e-8a9c-7fbb482e1b26">

   </P>


## Enabling HTTPS and certificate files SElinux context
1. Install Apache HTTP server and mod-ssl Module
    <p align="center">
      <img width="389" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/b8a52d64-5c30-4fcb-8029-3d8a7391d849">

    </p>
2. Generate Self-Signed SSL Certificate
   Create a public key certificate and private key pair using OpenSSL to establish secure HTTPS connections.
   <p align="center">
     <img width="646" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/b5a4879a-4009-47ca-b9ea-86650195700e">

      <img width="651" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/9d4e057e-865d-4087-9dc2-72f47b14caa5">

   </p>
   Fill in the information accordingly to generate a valid certificate.

   Your `server.key` and `server.crt` should look like these:
   <p align="center">
     <img width="452" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/5d118aa9-3c41-4b32-8986-d226cca113a0">
     <img width="644" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/b66a082e-cb2b-453a-845c-9b63daaf813f">
   </p>
4. Store certificate and private key with proper SELinux context.
   Proper context, ownership, and permissions are important for SELinux to enforce all the security rules.
  <p align="center">
    <img width="646" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/86b3bf08-9a14-4bd4-84ae-1726d10ecc51">
    <img width="649" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/b2d0e2df-7152-4b61-9636-3260c17b41e3">
    <img width="643" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/e4c5a3a5-a34b-4e6f-9018-d3d932a86268">
    <img width="646" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/1d42e915-4063-4620-825d-6a685e22bd96">
    <img width="646" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/3b1930be-7946-4b82-9ee8-d80cf3ad1252">
  </p>

5. Configure Apache to use SSL/TLS
   Modify the SSL config file to specify the locations of the certificate and private key files for secure HTTPS connections.
   <p align="center">
     <img width="446" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/4c5cf1a3-dfd1-45b8-86a6-3d9b5f082c74">

   </p>

6. Modify firewall rules for HTTPS communications
   Update firewall settings to permit HTTPS communications via port 443 so that external clients are able to establish secure connections to the web server over HTTPS.

7. Verify SSL configuration and certificate usage
   Let's do the test!
   If everything is configured correctly, the server will respond with the SSL certificate details, including the information provided during certification generation. And a secure connection is established throughoutly.
      <p align="center">
     <img width="646" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/8fdc4137-7ac0-4676-bd25-0a9f87427cc4">

   </p>

## SELinux context for web server CONTENT.
## Create a sudo transition for a cy5130_u to become webadm_r
## Create a content page for the user
