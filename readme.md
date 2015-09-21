# wdidc.org

## Services

See [the List](https://docs.google.com/spreadsheets/d/1whsU1PPMqAosbNeNI6LZS4V13FTfGyyTTcj8yYO6ezI/edit#gid=0)

### File server

Visible at http://locker.wdidc.org/

You may connect to this server via an SFTP client with your user account credentials:

- user: your username
- host: www.wdidc.org
- path: /var/www/locker.wdidc.org/


### git

Git repos are stored in `/home/git/`

Repositories can be cloned using the SSH protocol: `git clone git@git.wdidc.org:APIs/assignments.git`

You can view existing repositories at http://git.wdidc.org/

To create a new repo, initialize a bare repository in the git user's home directory, or:

```
ssh git@git.wdidc.org "git init --bare name-of-repo.git"
```

### web server

The default web server is nginx. Configuration files for virtual hosts
are stored in `/etc/nginx/sites-enabled/`

Application files are stored in `/var/www`

### user accounts

Everyone belongs to the www-data group.
