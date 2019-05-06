# zoom.us snap package
===========================

## Abandoned 

This repo is abandoned and will be removed soon. It seems to me that zoom.us is officially maintained on flathub.
So for people who want to use zoom.us please go to flathub instead.
https://flathub.org/apps/details/us.zoom.Zoom



"zoom.us" is a client to connect https://zoom.us/ - a conference website.
However, official site provides a deb package for installation which is
quite dangerous for the system. Now Ubuntu provides a new packaging format
called snap which can pack all its dependencies inside.
And it runs on a squashfs which is isolated to the system rootfs.
Also snap packages can be used on many other distros.

This document describes how to make a snap package for zoom.us client.
However, we cannot re-distribute the snap package to you because of
the proprietary license of that client so please make the snap package
by yourself.

## Create a pbuilder environment of Ubuntu Xenial.

Because we want to install some deb packages and
we don't want to taint your system, please create a pbuilder
environment for Ubuntu Xenial at first by the following steps:

 1. Create xenial chroot environment

    ~~~
    pbuilder-dist xenial create
    ~~~

Note: pbuilder-dist is in ubuntu-dev-tools package. Please install it first.

    sudo apt install ubuntu-dev-tools

## Building the snap package inside the chroot environment.

 1. Make an empty directory in /tmp
    ~~~
    mkdir /tmp/p1
    ~~~
 
 2. Login into the chroot environment. Also bindmounts /tmp/p1 so that
    directory is shared between chroot and the outside system.
    ~~~
    pbuilder-dist xenial login --bindmounts /tmp/p1
    ~~~

 3. Install some packages
    ~~~
    apt-get install git snapcraft
    ~~~

 4. Move to the tmp directory
    ~~~
    cd /tmp/p1
    ~~~

 5. Download our source
    ~~~
    git clone https://github.com/grandpaul/zoom.us-snap.git
    ~~~
    
 6. Go into the project directory
    ~~~
    cd zoom.us-snap
    ~~~

 7. Download the client deb from the official site and put into the deb
    subdirectory. 
    ~~~
    mkdir deb
    # download the deb and drop the deb inside
    ~~~

 8. Make deb subdirectory a rough local repo
    ~~~
    cd deb
    dpkg-scanpackages -m . | gzip -c > Packages.gz
    cd ..
    ~~~

 9. Add local repo to /etc/apt/sources.list. Note: this is the sources.list
    inside your chroot, not the one on host.
    ~~~
    echo "deb [trusted=yes] file:///tmp/p1/zoom.us-snap/deb ./" > /etc/apt/sources.list.d/50-zoom-client.list
    ~~~
    
 10. Install zoom inside chroot
     ~~~
     apt update
     apt install zoom
     ~~~
    
 11. Run snapcraft
     ~~~
     snapcraft
     ~~~

Now you have your snap package generated in /tmp/p1/zoom.us-snap

## Install and use.

 * Install snapd on your host system to be able to use snap packages.
    ~~~
    sudo apt install snapd
    ~~~

 * To install the snap package. Please use the following command:
    ~~~
    sudo snap install --dangerous zoom_2.0.98253.0707_amd64.snap
    ~~~

 * To run zoom client, please use the command:
    ~~~
    snap run zoom
    ~~~
