# Dockerix

Local [Docker for Mac](https://docs.docker.com/docker-for-mac/install/)-centered web development environment to run older and newer Drupal (WordPress etc.) (multi-)sites in parallel.

- Debian 9
- Apache 2.4
- MariaDB 10
- PHP 5.6 & PHP 7.1

The underlying base image can be found on Docker Hub: https://hub.docker.com/r/leymannx/apache-multiphp/

You are supposed to put your CMS files into <code>./www/php56</code> or <code>./www/php71</code> depending on the PHP version you need to run the site with.

The base image comes with:
- <code>drush8</code> command for older Drupal projects
- <code>drush</code> command for [Drush Launcher](https://github.com/drush-ops/drush-launcher)
- <code>composer</code>
- <code>npm</code>

The databases can be accessed with <code>mysql -uroot -proot -hmariadb</code> from within the base container. When it comes to connecting to a database you have to define <code>mariadb</code> as host (where it usually would be <code>localhost</code>).

## Possible routine

Tested with Docker for Mac (Stable channel Version 17.12.0-ce-mac49)

1. <code>$ git clone https://github.com/leymannx/dockerix.git dockerix</code>
2. <code>$ cd dockerix</code>
3. <code>$ docker-compose up -d</code>

Now visit [http://localhost:8856](http://localhost:8856) or [http://localhost:8871](http://localhost:8871) in your browser to see the corresponding PHP info files.

To run commands inside the base container get inside it with:

<code>$ docker exec -it dockerix_multiphp_1 bash</code>

From there maybe create a databases:

1. <code>$ mysql -uroot -proot -hmariadb -e "create database drupal7"</code>

Or run drush commands:

1. <code>$ cd /var/www/php56</code>
2. <code>$ drush8 dl drupal-7</code>
3. <code>$ mv drupal-7.56 drupal7</code>
4. Update <code>RewriteBase</code> in <code>.htaccess</code>

Now visit [http://localhost:8856/drupal7](http://localhost:8856/drupal7) in your browser and start the installation routine.

## Performance

Honestly without any fine-tuning page load times can be super bad with the initial **version 1** setup. Time-to-First-Byte of up to 10 seconds per node. This delay is caused by Docker for Mac's built-in volume mount and file sync process. You could simply fix it by not mounting any host volumes. But that really can't be it.

Starting with **version 2** I've included two of cweagans' [bg-sync](https://hub.docker.com/r/cweagans/bg-sync/) containers. They dramatically improve page load times. They only need a little while for the first sync ( 5-15 minutes). From then on Time-To-First-Byte will be less then a second. Pretty good. Unfortunately this limits this version to Mac OS and Docker for Mac. You may want to try other background sync approaches on a differing host.

Note: When using this image as is (with cweagans/bgsync) and if you ever have to run <code>docker-compose down</code> pay attention to prevent the initial and time-consuming filesync on the next run by restarting the containers with <code>docker-compose restart -d</code> in their already-synced state (which normally is done automatically on your Mac's wake-up or boot).

Also before you shut down the containers or your Mac you may have a look at the Activity Monitor and look if the <code>hyperkit</code> process is not super-active anymore (CPU usage > 100%). If it is, you may like to wait a short while until it's done (CPU usage ~ 1-5%). Which simply means to wait that bg-sync is done syncing and there will be no/less conflicts on the next restart. 

## restart: always?

No. Unfortunately there seems to be a [bug](https://github.com/cweagans/docker-bg-sync/issues/13) with Unison or with the bg-sync container that resets the syncing behavior after restarting Docker for Mac (or your Mac respectively). So for now you have to restart the containers always manually. Navigate to the folder where the <code>docker-compose.yml</code> is located and run <code>$ docker-compose start</code>.

## Linux / Windows

To check out a non-Mac centered version of this repo you might want to fork an older version that doesn't rely on cweagans/bgsync and start from there:

<code>$ git clone https://github.com/leymannx/dockerix.git --branch 0.1.1 dockerix</code>

## Kudos to

- [Ruben Egiguren](https://github.com/keopx/docker-lamp)! (for his Docker LAMP)
- [Michael Moravec](https://pehapkari.cz/blog/2017/03/27/multiple-php-versions-the-easy-way/)! (for his article on multiple PHP versions and the comments there)
- [Cameron Eagans](https://github.com/cweagans/docker-bg-sync)! (for bg-sync)
