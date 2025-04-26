# Killercode Jail Lab

## Create a Jail

Your team is planning on building a bastion system. You've read that the first part of properly bastioning systems is to jail, or lock users down. You have decided to build a jail and test functionality.

Build a jail at /var/chroot/ .

Add basic framework to the jail so that you can populate it with functionality.

Change your root to /var/chroot and see what functionality you have.

### Solution

Create the chroot filesystem.
`mkdir /var/chroot`

Create the directory structure of the jail.
mkdir -p /var/chroot/{bin,lib64,dev,etc,home,usr/bin,lib/x86_64-linux-gnu}

Check the directories have been created.
ls -l /var/chroot

What directories do you see? 
Is there anything about these directories that you know about that would make sense to give a user?

Move in the minimum executables for chroot to work properly.
cp /usr/bin/bash /var/chroot/bin/bash

The binary cannot work properly without the link libraries. Copy those in to get basic bash shell functionality.

for package in $(ldd /bin/bash | awk '{print $(NF -1)}'); do cp $package /var/chroot/$package; done
You can ignore the error about stat-ing the one file linux-vdso.so.1 it won't affect the rest of the lab.

Once you've done this you can jail your user and see what functionality you have in the jail.

Check your current bash process

echo $$
Jail your user in /var/chroot and see what functionality you have.

chroot /var/chroot
Check your current bash process

echo $$
If they're different you're in a jailed bash environment now.

Try any commands you know in Linux, what has happened to your user? Where are your commands at? can you use uptime? ping?

Spend time getting familiar with jails and chroot

Exit the jail

exit
Perform some system commands to make sure you've left the jail. How did your command prompt change when you were jailed? Why do you think that is?

ping -c 1 www.google.com
Hit submit to move onto the next part of the lab.


---


## Populate the jail

You've setup the jail and verified that there is little to no functionality in the jail. We need to make the jail useful for a user. We have to give the basic pieces for the user to have functionality we want to give them.

Copy in the correct system files for user functionality.

Copy in the correct binaries you want to give them: curl, and ssh. (you already gave bash)

Copy in the link libraries needed for curl, and ssh to work properly. (you already gave them bash)

Copy in the right environment files and home directory that allow a user session to run commands properly.

### Solution

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
You've set up your environment so hit submit to move on and test.


---

## Verify jail functionality

We believe we have all the correct pieces in place, but now we need test that the user has capabilities we've given them and only those capabilities.

### Solution

Create user to test ssh is running in the environment.
useradd -m jailed
passwd jailed
Set password to something easy like 12345678

Jail your user and perform a curl test

chroot /var/chroot
curl www.google.com
Does this work? Why or why not? Can you curl to yahoo.com? Check your /etc/hosts and /var/chroot/etc/hosts to see why that is. Do you get DNS resolution in the jail as we set it up?

Exit the jail.

exit
Jail your user again and run a ssh test

chroot /var/chroot
ssh -l jailed 127.0.0.1
Enter the password and then verify that you're in the correct user. Are you still jailed? Exit out and see if your original user is still jailed. How do you exit that jail?

Hit submit to finish this lab.

---

Look at you, learning Linux Security! You created a jail, verified it was locked down, added functionality to it, and then put yourself in jail to see that a user really is jailed.

There is a lot of functionality to add or test. Please keep playing in this environment to see what additional things you can discover.