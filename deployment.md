# Deployment

## Initialize bare repo on server

    $ ssh git@git.wdidc.org "git init --bare name-of-repo.git"

## Push your code to that repo

    $ git remote add production git@git.wdidc.org:name-of-repo.git

## Clone this bare repo to a working directory

    $ ssh git@git.wdidc.org
    $ cd /var/www
    $ git clone ~/name-of-repo.git

## Deploying Rails apps

### Decide on an application server

Use `forever` for node apps.

Use `unicorn` for rack-based apps.

Pick a port to serve on in whatever configuration file is necessary for application server

### Try starting your app

    $ bundle install && rake db:create && rake db:migrate ...
    $ unicorn -c path/to/config/unicorn.rb -E development -D 

If directory not writeable, make sure directory exists and permissions allow
the git user to write to it.

### Configure a subdomain

    cd /etc/nginx/sites-enabled/

Create a new file with the same name as your subdomain.

```
server{
  listen 80;
  server_name subdomain.wdidc.org;
  root /path/to/public/folder;
  location / {
    try_files $uri @name_of_app;
  }
  location @name_of_app {
    proxy_set_header X-Forwarded-For $proxy_add_x_forward_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-Forwarded_Proto $scheme;
    proxy_redirect off;
    proxy_pass http://name_of_app;
    proxy_read_timeout 300s;
    proxy_send_timeout 300s;
  }
}
```

### Configure Proxy Server

    $ cd /etc/nginx/
    $ sudo vi nginx.conf

```
upstream name_of_app{
  server unix:/path/to/app/tmp/unicorn.sock fail_timeout=0;
}
```
    
### Restart Nginx

    $ sudo service nginx reload

 Errors?

    $ sudo nginx -t

### Visit site in browser

If you get "502 Bad Gateway", application server is not running.

### Other Errors?

    $ sudo tail /var/log/nginx/error.log
    $ tail /path/to/app/tmp/unicorn.log

### Automate deployment

#### Automate unicorn start and stop

    $ sudo cp /etc/init.d/unicorn_something 

#### Configure post-update hook

    $ cd /home/git/name-of-repo.git/hooks/
    $ mv post-update.sample post-update
    $ chmod +x post-update

```bash
# post-update 
cd /var/www/path/to/app
unset GIT_DIR
git pull origin master
sudo service unicorn_name_of_app restart

exec git update-server-info
```

## Deploying Node Apps

As git user...

- create bare git repo
- clone bare to /var/www as url
- cd to that folder
- install dependencies `npm install`
- try it out `npm start`
- forever start app.js
- add forever start to crontab
  - `crontab -e`
  - `@reboot /usr/local/bin/forever start /var/www...`
- configure virtual host
- cd /etc/nginx/sites-enabled
- sudo vim subdomain.wdidc.org


```
server{
  listen 80;
  server_name subdomain.wdidc.org;
  root /var/www/domain/public;
  location / {
    proxy_pass http://localhost:PORT/; 
    # whatever port the app is runnin on
  }
}
```
- sudo service nginx reload
- check it out!
- sudo nginx -t

