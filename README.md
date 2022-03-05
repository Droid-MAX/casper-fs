# casper-fs 👻
<img align="center" src="https://github.com/CoolerVoid/casper-fs/blob/main/doc/Screenshot_9.png?raw=true">
Casper-fs is a Linux kernel module generator for custom rules in file system(Hidden/unhidden files, protect files).
This program have two pricipal functions one to turn private files in hidden. The second function is to protect confidential files to prevent reading, writing and removal.

The motivation: in a bad situation, an attacker can read every file in your machine. But if you have Casper-fs custom module, 
the attacker will not find the hidden kernel module that have functions to turn any file in hidden and protect to bloack read/remove/edit actions.

My beginning purpose at this project is to protect my server, and now it is to protect my friends' machines.
When I talk to friends, I say peoples that don't know how to write low-level code. Using the Casper-fs, you can 
generate your custom kernel module for your secret files protection. The low-level programmer can write new templates for modules etc.


The first step, understand before the run
--

Verify if the kernel version is 3.x, 4.x, or 5.x:
```
uname -r
```

Clone the repository
```
git clone https://github.com/CoolerVoid/casper-fs
```

Enter the folder
```
cd HiddenWall/module_generator
```

Edit your file rules in directory module_generator/rules/fs-rules.yaml, the python scripts, use that file to generate a new firewall module.

```
$ cat module_generator/rules/fs-rules.yaml
binary_name: casperfs
module_name: Casper-fs custom module -
unhide_module_key: AbraKadabra
hide_module_key: Shazam
fake_device_name: usb15
unhide-hide-file-key: Alakazam
unprotect-protect-file-key: Sesame 
fs-rules: 
- hidden: 
   1: secret.txt
   2: my_vault.db
- protect:
   1: backup_httpd.log
   2: secret_img.iso
   3: secret_file.img
```
The array hidden and array protect, you can insert a lot another files elements on context for example:
```
- protect:
   1: backup_httpd.log
   2: secret_img.iso
   3: secret_file.img
   4: secret_file2.img
   5: secret_file3.img
```
If you want to study the static code to generate, look at the content at directory "templates".

The second step, generate your module
--

If you want to generate a kernel module following your YAML file of rules, follow that command:

```
$ python3 casper-fs-gen.py --rules rules/fs-rules.yaml
```
This action can generate a generic module with the rules of the fs-rules.yaml.



The third step, install your module.
--

If you use Fedora Linux, install kernel packages for developer:
```
# dnf update
# dnf install kernel-headers.x86_64 kernel-modules.x86_64 kernel.x86_64 kernel-devel kmod
```
On Ubuntu Linux:
```
apt install linux-headers-generic gcc make
```
To test module:
```
# cd output; make clean; make
# insmod casperfs.ko
```


The fourth step run your custom module.
--

* The password to turn casper-fs visible is "AbraKadabra".
* The password to turn the casper-fs invisible is "Shazam".
* The password to turn the secret files in hidden is "Alakazam" the same to turn to unhidden.
* The password to protect files or unprotect is "Sesame".

You need to send the password for your fake device, "usb15" for example to test hidden and unhidden resources on file system:
```
$ touch secret.txt
$ ls
-- no results--
$ echo "Alakazam" > /dev/usb15
$ ls
secret.txt
$ echo "Alakazam" > /dev/usb15
$ ls
-- no results--
```

To exit the module, you need to turn visible at the "lsmod" command ...

```
# echo "AbraKadabra" > /dev/usb15
# lsmod | grep casper
# rmmod casperfs
```

Random notes
--

Tested on ubuntu 16 and fedora 29 at kernels "3.x","4.x" and "5.x".


## Point of attention
The purpose of this tool is to use in pentest, take attention if you have a proper authorization before to use that. I do not have responsibility for your actions. You can use a hammer to construct a house or destroy it, choose the law path, don't be a bad guy, remember.


References
--

*Wikipedia Netfilter* 
https://en.wikipedia.org/wiki/Netfilter

*Linux Device Drivers* 
http://lwn.net/Kernel/LDD3/

*M0nad's Diamorphine* 
https://github.com/m0nad/Diamorphine/
