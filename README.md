# WordPress 5.4.1 cn deploy on heroku
## 安装 Installation

Clone the repository from Github

克隆下来，有点大，早点用代理或者服务器远程

    $ git clone git://github.com/movny/heroku-wp5.4.1cn.git

With the [Heroku gem](http://devcenter.heroku.com/articles/heroku-command), create your app

用命令行来操作，创建一个

    $ cd heroku-wordpress
    $ heroku create
    Creating strange-bird-1234... done, stack is cedar
    http://strange-bird-1234.herokuapp.com/ | git@heroku.com:strange-bird-1234.git
    Git remote heroku added

Add a database to your app

添加数据库

    $ heroku addons:create heroku-postgresql
    Creating HEROKU_POSTGRESQL_INSTANCE... done, (free)
    Adding HEROKU_POSTGRESQL_INSTANCE to strange-bird-1234... done
    Setting DATABASE_URL and restarting strange-bird-1234... done, v3
    Database has been created and is available
     ! This database is empty. If upgrading, you can transfer
     ! data from another database with pgbackups:restore
    Use `heroku addons:docs heroku-postgresql` to view documentation.

Promote the database (replace HEROKU_POSTGRESQL_INSTANCE with the name from the above output)

说白了就是添加环境变量`HEROKU_POSTGRESQL_INSTANCE`，值和`DATABASE_URL`一样

    $ heroku pg:promote HEROKU_POSTGRESQL_INSTANCE
    Promoting HEROKU_POSTGRESQL_INSTANCE to DATABASE_URL... done
    Ensuring an alternate alias for existing DATABASE... done, HEROKU_POSTGRESQL_COLOR
    Promoting HEROKU_POSTGRESQL_INSTANCE to DATABASE_URL on strange-bird-1234... done

Add the ability to send email (i.e. Password Resets etc)

这个没必要，需要验证电话号，还是国外的

    $ heroku addons:create sendgrid:starter
    Creating SENDGRID_INSTANCE... done, (free)
    Adding SENDGRID_INSTANCE to strange-bird-1234... done
    Setting SENDGRID_PASSWORD, SENDGRID_USERNAME and restarting strange-bird-1234... done, v7
    Use `heroku addons:docs sendgrid` to view documentation.

Create a new branch for any configuration/setup changes needed

    $ git checkout -b production

Store unique keys and salts in Heroku environment variables. Wordpress can provide random values [here](https://api.wordpress.org/secret-key/1.1/salt/).

获取一下，填进去，也是环境变量

    heroku config:set AUTH_KEY='put your unique phrase here' \
      SECURE_AUTH_KEY='put your unique phrase here' \
      LOGGED_IN_KEY='put your unique phrase here' \
      NONCE_KEY='put your unique phrase here' \
      AUTH_SALT='put your unique phrase here' \
      SECURE_AUTH_SALT='put your unique phrase here' \
      LOGGED_IN_SALT='put your unique phrase here' \
      NONCE_SALT='put your unique phrase here'

Deploy to Heroku

部署

    $ git push heroku production:master
    -----> Deleting 0 files matching .slugignore patterns.
    -----> PHP app detected

     !     WARNING: No composer.json found.
           Using index.php to declare PHP applications is considered legacy
           functionality and may lead to unexpected behavior.

    -----> No runtime requirements in composer.json, defaulting to PHP 5.6.2.
    -----> Installing system packages...
           - PHP 5.6.2
           - Apache 2.4.10
           - Nginx 1.6.0
    -----> Installing PHP extensions...
           - zend-opcache (automatic; bundled, using 'ext-zend-opcache.ini')
    -----> Installing dependencies...
           Composer version 1.0-dev (ffffab37a294f3383c812d0329623f0a4ba45387) 2014-11-05 06:04:18
           Loading composer repositories with package information
           Installing dependencies
           Nothing to install or update
           Generating optimized autoload files
    -----> Preparing runtime environment...
           NOTICE: No Procfile, defaulting to 'web: vendor/bin/heroku-php-apache2'
    -----> Discovering process types
           Procfile declares types -> web

    -----> Compressing... done, 78.5MB
    -----> Launcing... done, v5
           http://strange-bird-1234.herokuapp.com deployed to Heroku

    To git@heroku:strange-bird-1234.git
      * [new branch]    production -> master

After deployment WordPress has a few more steps to setup and thats it!

## Usage

Because a file cannot be written to Heroku's file system, updating and installing plugins or themes should be done locally and then pushed to Heroku.

## Updating

Updating your WordPress version is just a matter of merging the updates into
the branch created from the installation.

    $ git pull # Get the latest

Using the same branch name from our installation:

    $ git checkout production
    $ git merge master # Merge latest
    $ git push heroku production:master

WordPress needs to update the database. After push, navigate to:

    http://your-app-url.herokuapp.com/wp-admin

WordPress will prompt for updating the database. After that you'll be good
to go.

## Deployment optimisation

If you have files that you want tracked in your repo, but do not need deploying (for example, *.md, *.pdf, *.zip). Then add path or linux file match to the `.slugignore` file & these will not be deployed.

Examples:
```
path/to/ignore/
bin/
*.md
*.pdf
*.zip
```
