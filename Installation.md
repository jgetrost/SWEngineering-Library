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

Installing your app
-------------------

1. Edit your project's `.csproj`.
Add `<PublishWithAspNetCoreTargetManifest>false</PublishWithAspNetCoreTargetManifest>` to the `<PropertyGroup>` section of the `.csproj` file.

2. If you are using built in authentication:

Add: 
```
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});
```
to your application's `Startup.cs` file before calling `app.UseAuthentication();`.

3. Publish your project.

4. Copy the published output to your EC2 instance.

5. Give ownership of your app files to the www-data user and group.
```
sudo chown -R www-data:www-data /app/files/directory
```
6. Set up system service for your app.
```
sudo nano /etc/systemd/system/kestrel-{application-name}.service
```

Put this content in the file:
```
[Unit]
Description={application-description}

[Service]
WorkingDirectory=/app/files/directory
ExecStart=/usr/bin/dotnet /app/files/directory/{application-name}.dll
Restart=always
RestartSec=10  # Restart service after 10 seconds if dotnet service crashes
SyslogIdentifier=dotnet-{application-name}
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

7. Enable the service.
```
    sudo systemctl enable kestrel-{application-name}.service
```

Manage the service using these commands:
```
    sudo systemctl start kestrel-hellomvc.service

    sudo systemctl status kestrel-hellomvc.service

    sudo systemctl restart kestrel-hellomvc.service

    sudo systemctl stop kestrel-hellomvc.service
```


Configure EC2 for MySQL
================================

1. Update Package List, Install MySQL, and Follow Guided Install.
```
    sudo apt-get update
    sudo apt-get install mysql-server
    mysql_secure_installation
```

2. Edit Configuration:
```
    sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

3. Change Bind address to 0.0.0.0:
```
    bind-address = 0.0.0.0
```

4. Restart MySQL:
```
    sudo service mysql restart
```

5. Login to MySQL using your root account:
```
    mysql -u root -p
```

6. Setup a local account. !Replace the myuser and mypass with your own!
```
    CREATE USER 'myuser'@'localhost' IDENTIFIED BY 'mypass';
    CREATE USER 'myuser'@'%' IDENTIFIED BY 'mypass';
```

7. Grant new user permissons on all databases. !Replace myuser with your own!
```
    GRANT ALL ON *.* TO 'myuser'@'localhost';
    GRANT ALL ON *.* TO 'myuser'@'%';
```
