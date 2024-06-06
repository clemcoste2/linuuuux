# TP3 Admin : Vagrant



## 1. Une première VM

🌞 **Générer un `Vagrantfile`**

- vous bosserez avec cet OS pour le restant du TP
- vous pouvez générer une `Vagrantfile` fonctionnel pour une box donnée avec les commandes :

```powershell
PS C:\Users\lacos> mkdir ~/work/vagrant/test


    Répertoire : C:\Users\lacos\work\vagrant
[...]
PS C:\Users\lacos> cd ~/work/vagrant/test
PS C:\Users\lacos\work\vagrant\test> vagrant init Vagrantfile
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
PS C:\Users\lacos\work\vagrant\test> ls


    Répertoire : C:\Users\lacos\work\vagrant\test


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        12/01/2024     14:29           3462 Vagrantfile


PS C:\Users\lacos\work\vagrant\test> cat Vagrantfile
# -*- mode: ruby -*-
# vi: set ft=ruby :
[...]
```

🌞 **Modifier le `Vagrantfile`**

- les lignes qui suivent doivent être ajouter dans le bloc où l'objet `config` est défini
- ajouter les lignes suivantes :

```bash
lacos@ClemPC MINGW64 ~/work/vagrant/test
$ cat Vagrantfile | grep "config.vm.box_check_update = false"
  config.vm.box_check_update = false
lacos@ClemPC MINGW64 ~/work/vagrant/test
$ cat Vagrantfile | grep 'config.vm.synced_folder ".", "/vagrant", disabled: true'
  config.vm.synced_folder ".", "/vagrant", disabled: true
```

🌞 **Faire joujou avec une VM**

```powershell
PS C:\Users\lacos\work\vagrant\test> vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'generic/rocky9' could not be found. Attempting to find and install...
    [...]

PS C:\Users\lacos\work\vagrant\test> vagrant status
Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.
PS C:\Users\lacos\work\vagrant\test> vagrant ssh   
[vagrant@rocky9 ~]$ exit
logout
Connection to 127.0.0.1 closed.
PS C:\Users\lacos\work\vagrant\test> vagrant halt  
==> default: Attempting graceful shutdown of VM...
PS C:\Users\lacos\work\vagrant\test> vagrant destroy -f
==> default: Destroying VM and associated drives...
PS C:\Users\lacos\work\vagrant\test> vagrant status    
Current machine states:

default                   not created (virtualbox)

The environment has not yet been created. Run `vagrant up` to
create the environment. If a machine is not created, only the
default provider will be shown. So if a provider is not listed,
then the machine is not created for that environment.
PS C:\Users\lacos\work\vagrant\test> 
```

## 2. Repackaging



🌞 **Repackager la box que vous avez choisie**



```bash

[vagrant@rocky9 ~]$ sudo dnf update -y
Extra Packages for Enterprise Linux 9 - 
[...]  
Dependencies resolved.
Nothing to do.
Complete!
[vagrant@rocky9 ~]$ sudo dnf install -y python3 bind-utils nmap nc tcpdump vim traceroute nano dhclient
Last metadata expiration check: 0:01:00 ago on Sun 21 Jan 2024 04:13:49 PM UTC.
Package python3-3.9.18-1.el9_3.x86_64 is already installed.
Package bind-utils-32:9.16.23-14.el9_3.0.1.x86_64 is already installed.
Package nmap-3:7.92-1.el9.x86_64 is already installed.
Package nmap-ncat-3:7.92-1.el9.x86_64 is already installed.
Package tcpdump-14:4.99.0-7.el9.x86_64 is already installed.
Package vim-enhanced-2:8.2.2637-20.el9_1.x86_64 is already installed.
Package traceroute-3:2.1.0-16.el9.x86_64 is already installed.
Package nano-5.6.1-5.el9.x86_64 is already installed.
Package dhcp-client-12:4.4.2-19.b1.el9.x86_64 is already installed.
Dependencies resolved.
Nothing to do.
Complete!
[vagrant@rocky9 ~]$ sudo firewall-cmd --state
running
[vagrant@rocky9 ~]$ sudo cat /etc/selinux/config | grep SELINUX
 SELINUX= can take one of these three values:
 NOTE: In earlier Fedora kernel builds, SELINUX=disabled would also
SELINUX=disabled
 SELINUXTYPE= can take one of these three values:
SELINUXTYPE=targeted
```

🌞 **Ecrivez un `Vagrantfile` qui lance une VM à partir de votre Box**

```powershell
PS C:\Users\lacos\work\vagrant\test> cat .\Vagrantfile 
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "super_box.box"
end
PS C:\Users\lacos\work\vagrant\test> vagrant up        
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'super_box.box' could not be found. Attempting to find and install...
[...]
```

## 3. Moult VMs

Pour cette partie, je vous laisse chercher des ressources sur Internet pour les syntaxes. Internet regorge de `Vagrantfile` d'exemple, hésitez po à m'appeler si besoin !

🌞 **Adaptez votre `Vagrantfile`** pour qu'il lance les VMs suivantes (en réutilisant votre box de la partie précédente)

[Vagrantfile-3A](./partie1/Vagrantfile-3A)


🌞 **Adaptez votre `Vagrantfile`** pour qu'il lance les VMs suivantes (en réutilisant votre box de la partie précédente)

[Vagrantfile-3B](./partie1/Vagrantfile-3B)
