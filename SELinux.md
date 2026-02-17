Scenario: A web server needs to serve content from a non-standard directory (/webdata) and allow users to serve content from their home directories.

Conflict: Standard Linux permissions (chmod) are insufficient because SELinux blocks access based on context Types.

Resolution: Using semanage to update the policy database and setsebool to flip functional switches.

Scenario : We've just changed the Document root inside the configurations of httpd (/etc/httpd/conf/httpd.conf) pointing this root towards another directory - /webdata.

<img width="1099" height="295" alt="Captura de ecrã 2026-02-17 095630" src="https://github.com/user-attachments/assets/a2bec8b3-e4be-467a-9097-29bee8f59973" />


<img width="1089" height="45" alt="Captura de ecrã 2026-02-17 095727" src="https://github.com/user-attachments/assets/366410f6-f8b3-4793-839f-b2dbd9d4c2b3" />

when we CURL'ed the localhost to check if everything is alright the response was the html from the test page, which was odd to me, but after checking the logs (as you can see them above) we can see that there are permission denied logs over the directory destination where we would like to serve our content from (/webdata)

Even though the screenshot is slightly cut on an important piece of information (sorry!) it reads the following:

 (13)Permission denied: [client ::1:39112] AH00035: access to /index.html denied (filesystem path '/webdata/index.html') because search permissions are missing on a component of the path.

 The SE permissions over the /webdata would read the following : 

 unconfined_u:object_r:default_t:s0 

 Meaning that the permissions were given to an unconfined user, and there's default context over the directory (which is the most important thing to SELinux). This means to SELinux that this is not a directory that has a correct label to serve, or access httpd content.

 Now that I know this, we make it so that SElinux recognizes our new path as a correct path (in type), so that we can access it without any further questions from our "type hunter"

Before that, I want to make sure that we know that our curl in the future will be reading from the source where we want to serve our content: 

<img width="710" height="45" alt="Captura de ecrã 2026-02-17 100847" src="https://github.com/user-attachments/assets/cd3c351c-f5da-4eb2-b183-b63fdd777287" />


Then, we talk to our SELinux to let him know that changes are in order : 

<img width="1003" height="56" alt="Captura de ecrã 2026-02-17 101108" src="https://github.com/user-attachments/assets/234b5fb3-a40e-4857-8506-8223439b0d08" />


sudo semanage fcontext -a -t httpd_sys_content_t "/webdata(/.*)?"  -> We add (-a) a type (-t) of httpd_sys_content_t) towards not only the directory, but also making sure that any future content that's placed inside this directory, will inherit the same typing to avoid problems with SELinux.

afterwards : 
sudo restorecon -Rv /webdata

We restore context, recursively (meaning to search for any and all content inside /webdata, including also potential subdirectories) and restore their context following the above command. Every file and directory inside this folder should carry now the httpd_sys_content_t, preventing any further type mismatch is SELinux. the v (from -Rv) is the verbose flag, so we can not only watch, but be sure that the context is indeed changed from the default one, which was preventing SELinux from giving a permission green light.


So, with all of that, let's curl localhost, in hopes of finally seeing the sentence I made earlier : This is in webdata.

<img width="417" height="46" alt="Captura de ecrã 2026-02-17 101816" src="https://github.com/user-attachments/assets/f4e99696-c956-4133-9db9-e399aeecbe64" />


having this printed out means that we carried out the changes in the configuration file (/etc/httpd/conf/httpd.conf) correctly and managed SELinux to recognize that this folder is intended for httpd use.


