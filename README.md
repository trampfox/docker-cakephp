## Using Docker with CakePHP 3.x applications ##

It is based on [Tutum Dockerfile](https://github.com/tutumcloud/tutum-docker-php)
and on [php official Dockerfile](https://github.com/docker-library/php)

Still under dev...

1. **First install docker on your computer with the [official doc](https://docs.docker.com/installation/#installation).**

   On a mac, it will install boot2docker (a small vm) and you'll be able to launch Boot2docker app in the `Applications` folder that opens a Terminal bash.

   If you use boot2docker, get the ip of your boot2docker vm : `boot2docker ip` (ex: 192.168.59.104) and add it in your /etc/hosts file : `local.dev 192.168.59.104`

2. **Clone this repo anywhere in your computer, go in the folder and launch `docker build -t cake17/cakephp .`**

   You'll then have an image named `cake17/cakephp` on your computer with all libs
   needed to run CakePHP 3.x apps : ubuntu, apache2, mcrypt, etc...

   When it will be stable, I'll put this repo in the DockHub, so this 2. will not be necessary.

3. **In your app, add a Dockerfile like this:**

        FROM cake17/cakephp
        MAINTAINER yourname <yourname@example.com>

        # Add your commands here if necessary, for example a composer install
        RUN composer install

   Then build an image `docker build -t yourname/your-app-name .`

4. **Run your app**

   *Without Mysql*

        docker run -d --name your-container -v /local/path/to/app/:/var/www/html/ -p 80:80 yourname/your-app-name

   *With [Official Mysql Image](https://registry.hub.docker.com/_/mysql)*

        # Run a mysql container first
        docker run --name db -e MYSQL_ROOT_PASSWORD=root-password -e MYSQL_USER=yourname -e MYSQL_PASSWORD=yourpassword -e MYSQL_DATABASE=db_name -d mysql
        # Make a `docker inspect CONTAINER_MYSQL_ID | grep IPAddress` and change the host in the datasources in your app.php
        # And run your container with Mysql container linked
        docker run -d --name your-container -v /local/path/to/app/:/var/www/html/ -p 80:80 --link db:mysql yourname/your-app-name
        # in mode production you can omit the -v part as you won't change your files on the fly

5. **Launch your migrations**

   To create your tables. You can use the [Migrations cakephp-plugin](https://github.com/cakephp/migrations).

        # get CONTAINER_APP_ID with docker ps
        docker exec -it CONTAINER_APP_ID ./bin/cake migrations migrate

## TO DO ##

- How to fix permissions on tmp/ and logs/ folders
- mysql : create multiple databases on build and grant for one custom user
- mysql : fix the host to be localhost and not a random address given by docker


## License ##

Copyright (c) [2014] [cake17]

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
