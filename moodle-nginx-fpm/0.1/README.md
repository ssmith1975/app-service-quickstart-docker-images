# Moodle-nginx-fpm Docker Image 
This is a Moodle Docker image which can run on both [Azure Web App on Linux](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-linux-intro) and your Docker engines's host.

## Components
This docker image currently contains the following components:

1. Moodle
2. Nginx(1.14.0)
3. PHP (7.2.11)
4. MariaDB ( 10.1.32/if using Local Database )
5. Phpmyadmin ( 4.8.0/if using Local Database )

## How to configure to use Azure Database for MySQL with web app 
1. Create a Web App for Containers
2. Browse your site
3. Complete Moodle install and Enter the Credentials for Azure database for MySQL 

## How to configure GIT Repo and Branch
1. Create a Web App for Containers 
2. Add new App Settings

Name | Default Value
---- | -------------
GIT_REPO | https://github.com/azureappserviceoss/moodle-linuxappservice-azure
GIT_BRANCH | master

4. Browse your site

>Note: GIT directory: /home/site/wwwroot/moodle.
>
>Note: When you deploy it first time, Sometimes need to check config.php. RM it and re-config DB information is necessary.
>
>Note: Before restart web app, need to store your changes by "git push", it will be pulled again after restart.
>

## How to configure to use Local Database with web app 
1. Create a Web App for Containers 
2. Update App Setting ```WEBSITES_ENABLE_APP_SERVICE_STORAGE``` = true (If you like to keep you DB after restart.)
3. Add new App Settings 

Name | Default Value
---- | -------------
DATABASE_TYPE | local
DATABASE_USERNAME | some-string
DATABASE_PASSWORD | some-string
>Note: We create a database "azurelocaldb" when using local mysql . Hence use this name when setting up the app
>
>Note: Phpmyadmin site is deployed when using local mysql. Please go to 
http://[website]/phpmyadmin, and login with DATABASE_USERNAME and DATABASE_PASSWORD.
>
4. Browse your site 
5. Complete Moodle install

>Note: Do not use the app setting DATABASE_TYPE=local if using Azure database for MySQL


## How to turn on Xdebug
1. By default Xdebug is turned off as turning it on impacts performance.
2. Connect by SSH.
3. Go to ```/usr/local/etc/php/conf.d```,  Update ```xdebug.ini``` as wish, don't modify the path of below line.
```zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20170718/xdebug.so```
4. Save ```xdebug.ini```, 
5. Restart php-fpm by below cmd: 
```
# find gid of php-fpm
ps aux
# Kill master process of php-fpm
kill -INT <gid>
# start php-fpm again
php-fpm -D
```
5. Xdebug is turned on.

## Updating Moodle version , themes , files

If ```WEBSITES_ENABLE_APP_SERVICE_STORAGE``` = false  ( which is the default setting ) , we recommend you DO NOT update the Moodle core version , themes or files from Moodle admin dashboard.

Choose either one option to updated your files :

There is a tradeoff between file server stability and file persistence . Since we are using local storage for better stability for the web app , you will not get file persistence.  In this case , we recommend to follow these steps to update Moodle Core  or a theme or a Plugins version :
1.	Fork the repo https://github.com/azureappserviceoss/moodle-linuxappservice-azure
2.	Clone your repo locally
3.	Download the latest version of Moodle , plugin or theme being used locally
4.	Commit the latest version bits into local folder of your cloned repo
5.	Push your changes to the your forked repo
6.	Login to Azure portal and select your web app
7.	Click on Application Settings -> App Settings and change GIT_REPO to use your repository from step #1. If you have changed the branch name , you can continue to use linux-apservice . If you wish to use a different branch , update GIT_BRANCH setting as well. 

## Limitations
- Some unexpected issues may happen after you scale out your site to multiple instances, if you deploy a Moodle site on Azure with this docker image and use the MariaDB built in this docker image as the database.
- The phpMyAdmin built in this docker image is available only when you use the MariaDB built in this docker image as the database.
- Please Include  App Setting ```WEBSITES_ENABLE_APP_SERVICE_STORAGE``` = true  when use built in MariaDB since we need files to be persisted.

## Change Log

# How to Contribute
If you have feedback please create an issue but **do not send Pull requests** to these images since any changes to the images needs to tested before it is pushed to production. 
