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
