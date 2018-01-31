# Dockerix

Local Development Environment to run older Drupal 7 and newer Drupal 8 multisites in parallel

- Debian 9
- Apache 2.4
- MariaDB 10
- PHP 5.6 & PHP 7.1

The underlying base image can be found on Docker Hub: https://hub.docker.com/r/leymannx/apache-multiphp/

You are supposed to put your Drupal files into <code>./www/php56</code> or <code>./www/php71</code> depending on the PHP version you need to run the site with.

The base image comes with a <code>drush8</code> command for older Drupal instances and <code>drush</code> command for [Drush Launcher](https://github.com/drush-ops/drush-launcher).

The databases can be accessed with <code>mysql -uroot -proot -hmariadb</code>. When it comes to connecting to a databases you have to define <code>mariadb</code> as host (where it usually would be <code>localhost</code>).

## Possible routine

Tested with [Docker for Mac](https://docs.docker.com/docker-for-mac/install/)

1. <code>$ git clone https://github.com/leymannx/dockerix.git --branch 0.1.1 dockerix</code>
2. <code>$ cd dockerix</code>
3. <code>$ docker-compose up -d --force-recreate</code>

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
