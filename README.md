# Enhancing Web Server Security with SELinux: A Practical Guide
### What is SELinux?
SELinux stands for Security-Enhanced Linux which is a security architecture for Linux systems that allows administrators to have more control over users that are accessible to the system.
A set of rules are defined and used to tell the SELinux what can or cannot be accessed. Whenever a request is made to access an object, SELinux will check with an AVC where permissions are cached for subjects and objects.
So, this project will briefly demonstrate how SELinux works.
## Content Page
- [Objective](#objective)
- [Running SELinux](#running-selinux)
- [Changing httpd port mandatory policy](#changing-httpd-port-mandatory-policy)
- [Enabling HTTPS and certificate files SELinux context](#enabling-https-and-certificate-files-selinux-context)
- [SELinux context for web server CONTENT](#selinux-context-for-web-server-content)
- [Create a sudo transition for a demo_u to become webadm_r](#create-a-sudo-transition-for-a-demo_u-to-become-webadm_r)
- [Create a content page for the user](#create-a-content-page-for-the-user)

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
1. Create a custom `index.html`
```
<HTML>
  <header>
    <title>SELINUX Project</title>
  </header>
  <body>
    I am Wingna Tsoi and this is my SELINUX Project on date 2/21/2024.
  </body>
</html>
```
2. Move to `var/www/html/index.html`
3. Try `curl` to see if the new and custom `index.html` is shown. You should get sth like this, meaning the action is unpermitted (Great! SELinux is doing its job!):
<p align="center">
  <img width="474" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/678bbe1c-a175-448e-880b-23253655adcc">
  <img width="475" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/344d5c2c-6123-43bc-92b5-c3b8ba156eef">

</p>
4. Change the permission and context of the `index.html`.
   <p align="center">
     <img width="476" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/168929b2-20c8-4f21-804f-0e8d8f7d07f6">

   </p>
5. Connection succeeds now!
  <p align="center">
    <img width="475" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/fe6f7706-a17d-4f3b-966c-1e209d271958">

  </p>
  
## Create a sudo transition for a demo_u to become webadm_r
1. Create a new user: 
   Use the appropriate command to create a new user. For example:
     ```
     sudo useradd -m jennifer
     ```

2. Assign SELinux user and roles:
   - Assign the SELinux user `demo_u` to the newly created user `jennifer`. This can be done by modifying the `/etc/selinux/targeted/users_extra_users` file or using `semanage`.
     It is to ensure that operations of the user are controlled by SELinux policies. Also, assign the role webadm_r to grant additional permissions to the user.
   - Assign the role `webadm_r` to the user `jennifer`. This can be done by modifying the `/etc/selinux/targeted/contexts/users/user_u` file or using `semanage`.
   - Relabel the home directory of the user `jennifer` to ensure proper SELinux contexts:
     ```
     sudo restorecon -Rv /home/jennifer
     ```

3. Verify SELinux context:
   - Log in as the user `jennifer`.
   - Check the SELinux context using the command:
     ```
     id -Z
     ```
    - Ensure the SELinux context matches the expected one (`demo_u:staff_r:staff_t:s0:c0.c1023`).

4. Modify sudoers file:  
  Edit the sudoers file using `visudo`:
     ```
     sudo visudo
     ```  
  Add the following line at the end of the file to configure sudo transition for the user `jennifer`:
     ```
     jennifer    ALL=(ALL:ALL) TYPE=webadm_t ROLE=webadm_r /bin/sh
     ```

5. Test sudo transition:
Run a shell with sudo using the configured user and check the SELinux context:
     ```
     sudo /bin/sh
     id -Z
     ```
     ![image](https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/a5401e43-4ddf-465f-8d66-838cdaab5871)


## Create a content page for the user
1. Create public_html directory and index.html page:
   - Create a directory named `public_html` in the user's home directory (`/home/<username>`) to serve as the root directory for user-specific web content.
   - Create an `index.html` page within the `public_html` directory with personalized content, greeting the user with their username.

2. Edit userdir.conf:
   - Edit the `userdir.conf` file located at `/etc/httpd/conf.d/userdir.conf` to configure Apache to serve content from user directories.
   - It is to allow Apache to access user-specific web content stored in the `public_html` directories.
   - Your config file should look like this:
     <p>
       <img width="443" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/c056b9b1-f454-4057-9acd-2548b4693fd2">

     </p>

3. Restart httpd service:  
 Restart the `httpd` service to apply the changes made to the `userdir.conf` file and ensure that Apache is configured to serve user-specific web content.

4. Test access using curl:
   - Use `curl` to test access to the user's web content both through the default Apache page `https://localhost` or `http://localhost:<port>` and through the user's page `https://localhost/~<username>/` or `http://localhost:<port>/~<username>/`.
   - Access to the user's page should be forbidden for the time being due to SELinux context and permissions.

5. Edit SELinux context and permissions:  
Edit the SELinux context and permissions of the `public_html` directory to allow Apache to access user-specific web content instead of the default `/var/www/html`.
  Correct permission and context should look like this:
    ![image](https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/77c31879-c46d-4bc0-9264-5e3b85723efa)
    ![image](https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/295360a0-ab84-43a9-8000-6deab4efae92)


6. Set SELinux Boolean:  
Set the SELinux Boolean to grant Apache the necessary permissions to access user-specific web content.
     <p>
       <img width="575" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/50e8b281-91c4-4fdd-b986-4db8ff61ad52">

     </p>

7. Test access again:  
Use `curl` to test access to the user's web content again. You should be able to visit your own index page! Congrats！
   <p align="center">
     <img width="466" alt="image" src="https://github.com/jenniferwingna/SELinux-in-practice/assets/116328799/9f91cb0e-1d98-4462-858f-e400d984abd5">
     
   </p>


