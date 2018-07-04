### HTTP/2 Setup

**Requirements:**
 - PHP FPM - (event multiprocessing modul)
 - Installed Apache2

**Note:** HTTP/2 (h2) works only with HTPPS

#### Instalation:
**FPM:**
 - ```apt-get install libapache2-mod-fastcgi php7.0-fpm``` **(For version php 7.0)**
 - ```a2enconf php7.0-fpm```
 - ```a2enmod proxy proxy_fcgi```
 - ```a2dismod php7.0 mpm_prefork```
 - ```nano /etc/php/7.0/fpm/pool.d/www.conf``` :
    - Alter user, group, listen.owner i listen.group variables to ones that run your server.
        - Also here, you can set other configuration options such as number of procs.
    - Reboot apache: ```service apache2 restart```
        - If you get an error run ```service apache2 status```
        - Errors are possible if permissions are not setup correctly.
        - After correction run ```service apache2 restart```

***HTTP/2:***
- ```install software-properties-common``` 
- ```add-apt-repository -y ppa:ondrej/apache2```
- ```apt-get update```
 - If Apache is already installed:
    - ```apt-get --only-upgrade install apache2```
 - If Apache is not installed:
    - ```apt-get install apache2```
- ```a2enmod http2```
- ```service apache2 restart```

**Virtual Host:**

- Inside vhost file you need to define: ```Protocols h2 http:/1.1```
    - http:/1.1 is "fallback" protocol.
- ```service apache2 restart```

**HTTP/2 Server Push:**

- Use of server push is the push of additional resources to client such as scripts, styles and media files.
- Server push is based on preload mechanics.
- Server Push VS. Preload:
    - HTTP/1.1 enables preload trough:
        - HTML with ***rel*** as ***as*** (content type) attributes: 
            ```html
            <link rel="preload" href="main.js" as="script">
            ```
        - Request header:
        ```Link: <main.js>; rel=preload; as=script```
        - When server return HTML (first request), all other preload definde resources will be automatically requested via new request
        - Preload with HTTP/2
            ```Link: <main.js>; rel=preload; as=script; nopush```
            - No preload via HTML-a.
    - HTTP/2 server push is used like preload on HTTP/1.1
        ```html
            <link rel="preload" href="main.js" as="script">
        ```
        ```Link: <main.js>; rel=preload; as=script```
    - Important difference from preload is that the first request for html will return all other resources that are marked for preload via HTML or request header.
    - Note: Resources in header can be chained like:
        - ```Link: <main.js>; rel=preload; as=script, <main.css>; rel=preload; as=style```
