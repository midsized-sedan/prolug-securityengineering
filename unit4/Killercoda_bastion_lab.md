# Killercode Bastion Lab

## Create a jail on node01

Your team is planning on building a bastion system. You have tested functionality of the jail in a previous lab. For this lab you are going to simulate an air-gapped system connection via a bastion.

![bastion](.\bastion.png)

Build a jail on node01 at /var/chroot/ .

Add basic framework to the jail so that you can populate it with functionality.

Change your root to /var/chroot and see what functionality you have.

### Solution

Ensure you are on node01.

ssh node01
Create the chroot filesystem.

mkdir /var/chroot
Create the directory structure of the jail.

mkdir -p /var/chroot/{bin,lib64,dev,etc,home,usr/bin,lib/x86_64-linux-gnu}
Check the directories have been created.

ls -l /var/chroot
What directories do you see? Is there anything about these directories that you know about that would make sense to give a user?

Move in the minimum executables for chroot to work properly.

cp /usr/bin/bash /var/chroot/bin/bash
The binary cannot work properly without the link libraries. Copy those in to get basic bash shell functionality.

for package in $(ldd /bin/bash | awk '{print $(NF -1)}'); do cp $package /var/chroot/$package; done
Copy over the system files.

for file in passwd group nsswitch.conf hosts; do cp /etc/$file /var/chroot/etc/$file; done
Now that you've pulled those base files over copy in the binaries of commands we're going to give the jailed user.

for binary in bash ssh curl; do cp /usr/bin/$binary /var/chroot/usr/bin/$binary; done
Now that those are in the correct place, everything that those binaries use as a link library can be found with the ldd command. Test this out.

ldd /usr/bin/bash
This shows all the link libaries for bash. We need to copy them in for curl and ssh, we did bash in the last step.

for package in $(ldd /usr/bin/ssh | awk '{print $(NF -1)}'); do cp $package /var/chroot/$package; done
for package in $(ldd /usr/bin/curl | awk '{print $(NF -1)}'); do cp $package /var/chroot/$package; done
You can ignore the error about stat-ing the one file linux-vdso.so.1 it won't affect the rest of the lab.

Now that these files have been pushed, environment files have to exist for the bash shell to run properly.

mknod -m 666 /var/chroot/dev/null c 1 3
mknod -m 666 /var/chroot/dev/tty c 5 0
mknod -m 666 /var/chroot/dev/zero c 1 5
mknod -m 666 /var/chroot/dev/random c 1 8
mknod -m 666 /var/chroot/dev/urandom c 1 9
There's one last set of libraries that are needed for your user to have network functionality, they are the nss files. Copy them over.

cp -r /lib/x86_64-linux-gnu/*nss* /var/chroot/lib/x86_64-linux-gnu/
Exit node01

exit
You've set up your environment so hit submit to move on and start building the bastion portion.

---


## Build the bastion setup on node01

You've setup the jail it's time to put together the bastion portion.

Create a user on node01 called jaileduser

Create a user on controlplane called freeuser

Create a bastion.sh script to lock in the actions of jaileduser

Configure sshd to jail jaileduser.

### Solution

Create a user called freeuser on controlplane
useradd -m freeuser
Give the user a password of 12345678

passwd freeuser
You will have to enter it twice, it will not echo to the screen.

Create a user called jaileduser on node01

ssh node01
useradd -m jaileduser
Give the user a password of 12345678

passwd jaileduser
Jail the user's ssh sessions

vi /etc/ssh/sshd_config
Add the following lines at the bottom

Match User jaileduser
ChrootDirectory /var/chroot
Save the file with :wq or 'Shift + ZZ'

Restart the ssh daemon

systemctl restart ssh
Make a bastion.sh script and put it in the correct location for the jaileduser to execute it.

vi /root/bastion.sh
#!/bin/bash

#Input a number
read -n 2 -t 20 -p "Make your selection from the items below
You have 20 seconds

1. Connect to controlplane as freeuser.
2. Exit
" input

case $input in

1 )
  echo "You are being sent to Rocky1"
  /usr/bin/ssh freeuser@controlplane
  exit 0;
  ;;
2 )
  echo "You are leaving now."
  exit 0;
  ;;
* )
  echo "You have not entered a valid input"
  exit 0;
  ;;

esac

exit 0;

Save the file with :wq or 'Shift + ZZ'

Copy this file to the correct location in /var/chroot/bin/bastion.sh and ensure it is executable

cp /root/bastion.sh /var/chroot/bin/bastion.sh
chmod 755 /var/chroot/bin/bastion.sh
Ensure that /etc/passwd is correctly set for jaileduser to have /bin/bastion.sh as their shell.

vi /etc/passwd
use /jaileduser to search for their name and "i" to correct the 7th field to /bin/bastion.sh

Copy the correct /etc/passwd into the jail.

cp /etc/passwd /var/chroot/etc/passwd
Exit back to controlplane and hit submit for the next section.

exit

---

## Test bastion functionality

It is now time to test your bastion connection.

Connect from controlplane to node01 as the jaileduser user.

Verify functionality (or lack thereof) of your shell.

Finish the connection back to controlplane as freeuser and verify that you are indeed free of the jail.

### Solution

Ensure you are on controlplane
hostname
If not exit back to there.

Connect to node01 as the jaileduser

ssh jaileduser@node01
Enter your password 12345678

Follow your prompts and see if you can get into freeuser with password 12345678 back on controlplane.

id
hostname
If everything works, exit back out and see that you do not stop in the bastion, you leave the whole system. You should be back on controlplane.

exit
Test the timeout functionality of your server by sitting idle for 21 seconds. Test that you cannot leave that script/shell by any other means. If that all validates then continue on.

Hit submit to finish this lab.

---

Look at you, learning Linux Security! You created a jail, verified it was locked down, added functionality to it, and then implemented a full bastion around it.

You tested and verified that you were able to pass the user through to the far system, by way of your bastion host.

Reflect on how your understanding of this topic reflects the image here.

bastion.png

What do you need to go learn more about or spend more time with now to be more effective?