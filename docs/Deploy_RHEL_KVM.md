To Install on a RPM based Distro:

```
sudo yum install cockpit -y 

sudo systemctl enable --now cockpit.socket
```

Open A Web Browser
Go to your ip address, localhost + Collon 9090
I.E. localhost:9090 

Use your same username and password to log into Cockpit for the first time.

By Default you are logged in as the user.

If you want to do tasks as an elevated user you will need to click the blue button in the top middle of the screen.

Log in with your password and click Authenticate.

Now you can see that you are logged in with Administrative Access.

Next you want to click on Applications

Find the Entry for Machines, and Click the Install Button

You could also automate this by running the commands

```
sudo yum install libvirt cockpit cockpit-machines -y

sudo systemctl enable libvirtd --now
```

Once It’s installed we can move onto the next Tab

Once you click on the "Virtual Machine" option, you will see the following screen.
At the top of the Page you will see "Storage Pools" and "Networks".


Cockpit: Virtual Machines

Click on Storage Pools
Create a new one and call it “default”

To Bridge or Not to Bridge

Next you will need to decide if you will want to touch the rest of your network or just have your virtual machines talk on this 1 host.

If you are using the Ethernet, I’d suggest using the Bridge
The rest of your network will be accessible 

With the 2 prerequisites out of the way let's spin up our first VM’s

Now we want to install a couple of more tools,

```
sudo yum install libguestfs-tools libguestfs

```

This package lets us use preconfigured images

https://unix-supremacy.org/index.php/2020/04/04/enhance-system-security-using-red-hats-libvirtd-kvm-virtualization/#more-731 

This package lets us use preconfigured images
use this command to list them all

```
virt-builder --list
```

We are going to grab a centos 8 build

sudo virt-builder centos-8.2 -o rsyslog-server.qcow2 --formate qcows2 --update --hostname rsyslog1 --root-password password:SecurePassphrase4me --size 20G

OR you could download a RHEL IMAGE from access.redhat.com/downloads

Once you have downloaded the images
Move them to /var/lib/libvirt/images
Then set a root password or inject your ssh key into the image

Move them to /var/lib/libvirt/images

```
sudo mv ~/Downloads/rhel-8.4-x86_64-kvm.qcow2 /var/lib/libvirt/images/

sudo mv ~/Downloads/rhel-9.4-x86_64-kvm.qcow2 /var/lib/libvirt/images/

sudo mv ~/Downloads/rhel-9.5-x86_64-kvm.qcow2 /var/lib/libvirt/images/

```

Then elevate your privileges

```
sudo su
```

Then we change to the default location for the KVM images

```
cd /var/lib/libvirt/images
```

## RHEL 8.4 Example

Then I like  to make a copy of the image and lable it for my use case, like the following. 
This is an example for RHEL 8.4

```
cp rhel-8.4-x86_64-kvm.qcow2 rhce-control-node.qcow2

cp rhel-8.4-x86_64-kvm.qcow2 rhce-node1.qcow2

cp rhel-8.4-x86_64-kvm.qcow2 rhce-node2.qcow2
```

Then I customize it with a root password and uninstall cloud-init
This is an example for RHEL 8.4

```
virt-customize -a rhce-control-node.qcow2 --root-password password:HelloPassword2022 --uninstall cloud-init

virt-customize -a rhce-node1.qcow2 --root-password password:HelloPassword2025 --uninstall cloud-init

virt-customize -a rhce-node2.qcow2 --root-password password:HelloPassword2025 --uninstall cloud-init
```

## RHEL 9.4 Example

RHEL 9 does not allow root login via root by default

```
cp rhel-9.4-x86_64-kvm.qcow2 rhce94-bastion.qcow2

cp rhel-9.4-x86_64-kvm.qcow2 rhce94-node1.qcow2
```

RHEL 9 does not allow root login via root by default, so we will need to create a user

```
virt-customize -a rhce94-bastion.qcow2 --root-password password:HelloPassword2025 --uninstall cloud-init
```

Lets test this

```
virt-customize -a rhce94-bastion.qcow2 \
  --run-command 'useradd -m lab-user' \
  --run-command 'echo "lab-user:TempPassword2025" | chpasswd' \
  --run-command 'usermod -aG wheel lab-user'
```






