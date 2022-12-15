# Install thirdroom.io
## Download and deploy the front-end project from github

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](ninja.cooldev@gmail.com)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](ninja.cooldev@gmail.com)

### Prerequisite
Check node, npm and yarn
```
node -v
v18.7.0
npm -v
8.18.0
yarn -v
1.22.19
```
If you haven't installed one of these on the server, please install that.


### Build the front-end
Download from github
```
git clone https://github.com/matrix-org/thirdroom.git
```
And then go into directory `thirdroom` and install node modules.
```
cd thirdroom
npm i
```
After you've installed node.js locally, run the following commands.
```
npm install -g yarn
yarn
```
If the result is success, please change file `public/well-known/matrix/client` like following
```
{
    "m.homeserver": {
        "base_url": "https://ranobot.com:8008/"
    }
}

```
In `config.json` file
```
{
    "defaultHomeServer": "ranobot.com",
    "homeserverList": ["ranobot.com"],
    "onboardingVersion": 1
}
```

Build the project
```
npm run build
```
Or
```
yarn build
```

If you follow the above, the `dist` directory is created in the project directory.
In `dist` directory, rename `well-known` directory to `.well-known` directory.
```
cd dist
mv well-known/ .well-known/
```
And then rename `dist` directory to `ranobot` and then copy into `/var/www/html`
```
cd ..
mv dist/ ranobot/
cp -R ranobot/ /var/www/html/ranobot/
```

### Install and Configure Nginx Server.
Please read and follow the steps in `Install Nginx on Ubuntu.md` and `Increase Limit File Upload Size in NGINX.md`.
Add request headers into every request.
```
server {
    ...
    location / {
        ...
        add_header	Cross-Origin-Resource-Policy "cross-origin" always;
		add_header	Cross-Origin-Opener-Policy "same-origin" always;
		add_header	Cross-Origin-Embedder-Policy "require-corp" always;
		try_files $uri $uri/ /index.html?$args;
    }
    ...
}
```
The result like this.
```
server {

	root /var/www/html/ranobot;

	index index.html index.htm index.nginx-debian.html;

	server_name ranobot.com;

	client_max_body_size 102400M;
	client_body_timeout 300s;

	client_body_in_file_only clean;
	client_body_buffer_size 16K;
	client_body_temp_path /home/nginx/client_body_temp;

	location / {
		client_max_body_size 102400M;
		client_body_timeout 300s;

		client_body_in_file_only clean;
		client_body_buffer_size 16K;
		client_body_temp_path /home/nginx/client_body_temp;
		
		add_header	Cross-Origin-Resource-Policy "cross-origin" always;
		add_header	Cross-Origin-Opener-Policy "same-origin" always;
		add_header	Cross-Origin-Embedder-Policy "require-corp" always;
		try_files $uri $uri/ /index.html?$args;
	}
}

server {
    if ($host = ranobot.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


	listen 80 default_server;
	listen [::]:80 default_server;

	server_name ranobot.com;
    return 404; # managed by Certbot
}
```

#### Install and configure SSH (IMPORTANT!)
##### 1. SSH into the server
SSH into the server running your HTTP website as a user with sudo privileges.

##### 2. Install snapd
You'll need to install snapd and make sure you follow any instructions to enable classic snap support.
Follow these instructions on snapcraft's site to install 
    `install snapd`
##### 3. Ensure that your version of snapd is up to date
Execute the following instructions on the command line on the machine to ensure that you have the latest version of snapd.
```
sudo snap install core; sudo snap refresh core
```
##### 4. Remove certbot-auto and any Certbot OS packages
If you have any Certbot packages installed using an OS package manager like apt, dnf, or yum, you should remove them before installing the Certbot snap to ensure that when you run the command certbot the snap is used rather than the installation from your OS package manager. The exact command to do this depends on your OS, but common examples are sudo apt-get remove certbot, sudo dnf remove certbot, or sudo yum remove certbot.

##### 5. Install Certbot
Run this command on the command line on the machine to install Certbot.
```
sudo snap install --classic certbot
```
##### 6. Prepare the Certbot command
Execute the following instruction on the command line on the machine to ensure that the certbot command can be run.
```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
##### 7. Choose how you'd like to run Certbot
___Either get and install your certificates...___
Run this command to get a certificate and have Certbot edit your nginx configuration automatically to serve it, turning on HTTPS access in a single step.
```
sudo certbot --nginx
```
    ___Or, just get a certificate___
    If you're feeling more conservative and would like to make the changes to your nginx configuration by hand, run this command.
```
sudo certbot certonly --nginx
```
##### 8. Test automatic renewal
The Certbot packages on your system come with a cron job or systemd timer that will renew your certificates automatically before they expire. You will not need to run Certbot again, unless you change your configuration. You can test automatic renewal for your certificates by running this command:
```
sudo certbot renew --dry-run
```
The command to renew certbot is installed in one of the following locations:
- /etc/crontab/
- /etc/cron.*/*
- systemctl list-timers

##### 9. Confirm that Certbot worked
To confirm that your site is set up properly, visit https://ranobot.com/ in your browser and look for the lock icon in the URL bar.

### Sample nginx config file.
default - `/etc/nginx/sites-available/default` nigix config file for ranobot.com

## The neccessary files for building
- default
- fullchain.pem
- privkey.key or privkey.pem