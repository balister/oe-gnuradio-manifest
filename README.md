Repo Manifests for building systems based on meta-sdr
=============================================
This repository provides Repo manifests to setup the OpenEmbedded build system
with meta-sdr and some interesting boards

OpenEmbedded allows the creation of custom linux distributions for embedded
systems. It is a collection of git repositories known as *layers* each of
which provides *recipes* to build software packages as well as configuration
information.

Repo is a tool that enables the management of many git repositories given a
single *manifest* file.  Tell repo to fetch a manifest from this repository and
it will fetch the git repositories specified in the manifest and, by doing so,
setup an OpenEmbedded build environment for you!

Getting Started
---------------
1.  Install Repo.

    Download the Repo script.

        $ curl https://dl-ssl.google.com/dl/googlesource/git-repo/repo > repo

    Make it executable.

        $ chmod a+x repo

    Move it on to your system path.

        $ sudo mv repo /usr/local/bin/

2.  Initialize a Repo client.

    Create an empty directory to hold your working files.

        $ mkdir oe-repo
        $ cd oe-repo

    Tell Repo where to find the manifest

        $ repo init -u git://github.com/balister/oe-gnuradio-manifest.git -b stable

    A successful initialization will end with a message stating that Repo is
    initialized in your working directory. Your client directory should now
    contain a .repo directory where files such as the manifest will be kept.
    ***
    **Note**
    You can use the **-b** switch to specify the branch of the repository
    to use.  I develop on master so it might be iffy at times. Use the
    "stable" branch fornormal work. 

    The **-m** switch selects the manifest file (default is *default.xml*).

    To test out the bleeding edge, type:

        $ repo init -u git://github.com/balister/oe-gnuradio-manifest.git
    
    To get back to the known stable version, type:

        $ repo init -u git://github.com/balister/oe-gnuradio-manifest -b stable

    To learn more about repo, look at http://source.android.com/source/version-control.html 
    ***

3.  Fetch all the repositories.

        $ repo sync

    Now go put on the coffee machine as this may take 20 minutes depending on
    your connection.

4.  Initialize the OpenEmbedded Environment.

        $ TEMPLATECONF=/Full path to/meta-ettus/common/conf source ./oe-core/oe-init-build-env ./build ./bitbake

    This copies default configuration information into the build/conf*
    directory and sets up some environment variables for OpenEmbedded.  You may
    wish to edit the configuration options at this point.

5.  Build an image.

    This process downloads several gigabytes of source code and then proceeds to
    do an awful lot of compilation so make sure you have plenty of space (25GB
    minimum). Go drink some beer.

        $ export MACHINE="zedboard-zynq7" (default is ettus-e1xx)
        $ bitbake gnuradio-dev-image

    If everything goes well, you should have a compressed root filesystem
    tarball as well as kernel and bootloader binaries available in your
    *work/deploy* directory.  If you run into problems, the most likely
    candidate is missing packages.  Check out
    http://www.yoctoproject.org/docs/current/yocto-project-qs/yocto-project-qs.html#resources
    for the list of required packagaes for operating system. Also, take
    a look to be sure your operating system is supported:
    https://wiki.yoctoproject.org/wiki/Distribution_Support

6.  Build an SDK for cross compiling gnuradio on an x86 machine.

    Run:

        $ export MACHINE="zedboard-zynq7" (only if MACHINE is not already set)
        $ bibake -c populate_sdk gnuradio-dev-image

    When this completes the sdk is in ./build/deploy/imaghes/sdk as an .sh file
    you copy to the machine you want to cross compile on and run the file.
    It will default to installing the sdk in /usr/local, and you can ask it to
    install anywhere you have write access to.

Staying Up to Date
------------------
To pick up the latest changes for all source repositories, run:

    $ repo sync

Enter the OpenEmbedded environment:

    $ . oe-core/oe-init-build-env ./build ./bitbake

    If you forget to setup these environment variables prior to running bitbake,
    your OS will complain that it can't find bitbake on the path.  Don't try
    to install bitbake using a package manager, just run the command.

You can then rebuild as before:

    $ bitbake gnuradio-dev-image

Starting from Fresh
-------------------
So it is borked.  You're not really sure why.  But it doesn't work any more.

There are several degrees of *starting fresh*.

 1. clean a package: bitbake <package-name> -c cleansstate
 2. re-download package: bitbake <package-name> -c cleanall
 3. destroy everything but downloads: rm -rf build (or whereever your sstate and work directories are)
 4. destroy it all (not recommended): rm -rf build && rm -rf sources
There are several degrees of *starting fresh*.

Customize
---------
Sooner or later, you'll want to customize some aspect of the image either
adding more packages, picking up some upstream patches, or tweaking your kernel.
To this, you'll want to customize the Repo manifest to point at different
repositories and branches or pull in additional meta-layers.

Clone this repository (or fork it on github):

    $ git clone git://github.com/balister-oe-gnuradio-manifest.git

Make your changes (and contribute them back if they are generally useful :) ),
and then re-initialize your repo client

    $ repo init -u <file:///path/to/your/git/repository.git>

