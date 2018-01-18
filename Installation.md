Configure EC2 for .NET Core 2.0
================================
This document will list the steps necessary to run a .NET Core web application on an Ubuntu 16.04 EC2 instance on AWS.

Installing .NET Core (Ubuntu 16.04)
------------------------------------
1. Download and register the Microsoft product key.
```
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
```

2. Set up the host package feed and update repository listings.
```
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
```

3. Install .NET Core
```
sudo apt-get install dotnet-sdk-2.1.3
```

4. Run the `dotnet --version` command to prove the installation succeeded.

Installing nginx
----------------
1. Install nginx
```
sudo apt-get install nginx
```

2. Start nginx
```
sudo service nginx start
```

3. Edit configuration for reverse proxy to .NET application.
```
sudo nano /etc/nginx/sites-available/default
```

4. Replace `/etc/nginx/sites-available/default` with:
```
server {
    listen 80;
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $http_host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

5. Test the syntax of `/etc/nginx/sites-available/default/`.
```
sudo nginx -t
```

6. Reload nginx configuration.
```
sudo nginx -s reload
```
