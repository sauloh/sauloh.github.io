---
layout: post
title:  "Metasploitable 3 - Building it on Linux"
date:   2018-05-26 08:16:33 -0300
categories: tutorial metasploitable
---

It's been about two years since the new version of Metasploitable was released,
now they came up with a new great concetp and I just decided to play with it.

[Metasploitable][metasploitable-gh] used to be ISO Ubuntu image built with lots of vulnerable
services. The main goal of this, was to help people practice on how to use
metasploit and try new tools and exploits. 

The only drawback of Metasploitable, was being difficulty to improve and grow.
Since it was an ISO image, the community coudn't easily contribute with new
vulnerable softwares, and it was also hard for the maintainers to keep building
ISOs every other day. That's why it took about 4 years to go from Metasploitable
2 tou Metasploitable 3. But know, they came up with great solution.

The new Metasploitable 3 has a dynamic concept. You don't download an
pre-built ISO as before, instead you clone a github project with scripts that will download the base
ISO of the OS it will use, and automaticaly installs the OS and the vulnerable services inside it. That
makes it possible for easy contributions with new vulnerable projects and new
operational system.

By the time I'm writing this post, Metasploitable 3 has a
Windows 2008 and a Ubuntu 1404.

While creating the virtual machines, I went through some issues, that's why I'll write a
quick tutorial with a few tips if you also want to try it.

## Installing Metasploitable 3 on Linux

### Clone the project from github

First thing you need to do its to clone the project from github and enter the
project dir.

{% highlight bash %}
$ git clone https://github.com/rapid7/metasploitable3.git
$ cd metasploitable3
{% endhighlight %}

### Install and Configure Pre-requisites

Before starting the installation, you need to have a few things installed in the
right versions. The versions of the softwares might change from time to time, so
check out the 'build.sh' script to see the right version.

* [Packer][vbox]
* [Vagrant][vagrant]
* [VirtualBox][vbox]
* Internet Connection

![Reqs Versions]({{ "/assets/metasploitable3_reqs.png" | absolute_url }})

I won't go into the details on how to install them as it should be
really straight forward. But I would like to draw your attention to some things.

Metasploitable requires you to have at least 65 GB of available space on your
drive. After completing the inttallation, I noticed it took me only about 15 GB,
which is a lot less. That been said, you should know that the default Vagrant
installation, saves our your boxes to your home directory '.vagrand.d/boxes'.
That's why, if you use a different partition for data (like I do), you will need
Vagrant saving the file to a different location. 

You have 2 ways of doing that. Either configuring Vagrant to use a different
location, or just creating a symbolic link to a different location. I went with
the second option because I didn't want to mess with vagrant default configurations.

#### Creating symbolic link to vagrant
First move vagrant files to your media directory:

Ex:
{% highlight bash %}
$ mv /home/john/.vagrant.d /media/data/vagrant.d
{% endhighlight %}

And then create a link to the new location:

Ex:
{% highlight bash %}
$ ln -s /media/data/vagrant.d /home/john/.vagrant.d 
{% endhighlight %}

#### Install vagrant plugins

Another thing you will need to get Metasploitable running, are some vagrant
plugins.

{% highlight bash %}
$ vagrant plugin install vagrant-reload
$ vagrant plugin install winrm
$ vagrant plugin install winrm-elevated
{% endhighlight %}

Now you are ready to build your metasploitable image.

### Building Virtual Machines

Now all you have to do is to use the build script and wait for arround 40minutes
to 1 hour. It will download the clean ISOSs, and then install the vulnerable
services. In the mean time, you will see the Virtual Box window open.

{% highlight bash %}
$ ./build.sh ubuntu1404
$ ./build.sh windows2008
$ vagrant up 
{% endhighlight %}


### Vagrant Up

After the build is fininshed, set the machine up. You will also be able to see
and control it from the Virtual Box manager.
{% highlight bash %}
$ vagrant status
$ vagrant up 
{% endhighlight %}


You should have your enrironmt all set up by now. 

![Images in Vbox]({{ "/assets/metasploitable_vbox.png" | absolute_url }})

## Troubleshooting

For some reason I haven't figured out yet, the build script didn't build the
Windows VM, and also the 'vagrant up' command were not working.
If you face a problem like this one, you just need to mannually create the Virtual Machine. 

The build script, will create a .box file in the packer directory inside
metasploitable. Use the following command to create the box.ovf file.

{% highlight bash %}
$ vagrant box add packer/builds/windows_2008_r2_virtualbox_0.1.0.box --name metasploitable3-win2k8
{% endhighlight %}

1. After that, you will find your box.ovf file in your .vagrant directory under the
boxes/metasploitable3-win2k8/0/virtualbox/. Open Virtual Box and choose Import
Appliance under the File menu. 
2. Locate the box.ovf file at
   .vagrant.d/boxes/metasploitable3-win2k8/0/virtualbox under your home
directory.

![Importing Vbox]({{ "/assets/importing_vbox.png" | absolute_url }})

And that's it. Now you are free to try your new tools and improve your hacking
skills!
