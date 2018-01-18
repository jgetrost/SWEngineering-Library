Configure EC2 for .Net Core 2.0
================================

Installing .Net Core (Ubuntu 16.04)
------------------------------------

Register the Microsoft Product Key
'''
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
'''
