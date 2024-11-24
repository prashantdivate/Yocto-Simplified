## 01- Yocto Project Introduction
---------

### What is Yocto?

Yocto project provides open source, high quality infrastructure and tools to help developers create their own custom Linux distributions for any hardware architecture.

### History

Founded in 2010,  in an effort to reduce work duplication, provide resources and information catering to both new and experienced users.

collaboration of
        many hardware manufacturers
        open source operating systems vendors and
        electronics companies

Yocto is also a project working group of the Linux Foundation


### Advantages of Yocto Project

1. Widely Adopted Across the Industry
        Semiconductor, operating system, software, and service vendors exist whose products and services adopt and support the Yocto Project.
        Eg. Intel, Facebook, arm, Juniper Networks, LG, AMD, NXP, DELL

2. Architecture Agnostic
        supports Intel, ARM, MIPS, AMD, PPC and other architectures
        chip vendors create and supply BSPs that support their hardware
        if you have custom silicon, you can create a BSP that supports that architecture
        the Yocto Project fully supports a wide range of device emulation through the Quick EMUlator (QEMU)

3. Images and Code Transfer Easily
        Yocto Project output can easily move between architectures without moving to new development environments.

4. Flexibility
        Through customization and layering, a project group can leverage the base Linux distribution to create a distribution that works for their product needs.

5. Ideal for Constrained Embedded and IoT devices
        Unlike a full Linux distribution, you can use the Yocto Project to create exactly what you need for embedded devices
        You only add the feature support or packages that you absolutely need for the device

6. Uses a Layer Model
        The Yocto Project layer infrastructure groups related functionality into separate bundles.
        You can incrementally add these grouped functionalities to your project as needed
        Allows you to easily extend the system, make customizations, and keep functionality organized.


feel free to explore - https://www.yoctoproject.org/

### Why to use Yocto?

* To configure the Linux according to our specs. 
* The Image contains what we need.
* No unwanted extra packages. 
* Small Image Size.

### What is the  Work-flow?

For now, not going in detail in Work-flow. Will see later in detail.

https://www.yoctoproject.org/software-overview/

In Later videos, we will try to understand the work flow in detail.

### Host PC Requirements

* Minimum 50 GB free space
* Minimum 4 GB RAM
* Git 1.8.3.1 or greater
* tar 1.27 or greater
* Python 3.4.0 or greater.

```NOTE:``` Packages and package installation vary depending on your development system

#### Supported Linux Distros

- Ubuntu 18.04 (LTS)
- Ubuntu 20.04 (LTS)
- Ubuntu 22.04 (LTS)
- Fedora 34
- Fedora 35
- AlmaLinux 8.5
- Debian GNU/Linux 10.x (Buster)
- Debian GNU/Linux 11.x (Bullseye)
- OpenSUSE Leap 15.3

**I am using Ubuntu 18.04 (LTS)**

### Setup Host PC

I would recommend to use docker image as it is useful for team collaboration, we will use [this docker image repo](https://github.com/prashantdivate/docker-yoctobuilder) for our development


### Building Image

#### What is Poky?

Poky is a reference distribution of Yocto Project. The word "reference" is used to mean "example" in this context.

Yocto Project uses Poky to build images (kernel, system, and application software) for targeted hardware.

At the technical level it is a combined repository of the components

* some OE components(oe-core)
* bitbake
* demo-BSP's
* helper scripts to setup environment
* emulator QEMU to test the image

![poky](resources/poky.png)


**``Note:``** Poky does not contain binary files,it is a working example of how to build your own custom Linux distribution from source.

#### What is difference between poky and Yocto 

The exact difference between Yocto and Poky is Yocto refers to the organization ( like one would refer to 'canonical', the company behind Ubuntu ), and Poky refers to the actual bits downloaded ( analogous to 'Ubuntu' ) 


#### Workflow of Yocto Project

**<ins>Step-1:</ins>** Download the Poky Source code

```$ git clone git://git.yoctoproject.org/poky```

**<ins>Step-2:</ins>** Checkout the latest branch/release (zeus)

```$ git checkout scarthgap```

**<ins>Step-3:</ins>** Prepare the build environment

Poky provides you a script 'oe-init-build-env', which should be used to setup the build environment

script will set up your environment to use Yocto build system,including adding the BitBake utility to your path

```$ source poky/oe-init-build-env [ build_directory ]```

Here **build_directory** is an optional argument for the name of the directory where the environment is set.

in case it is not given , it defaults to **"build"**

The above script will move you in a build folder and create two files ( local.conf, bblayers.conf ) inside conf folder

**<ins>Step-4:</ins>** Building Linux Distribution

```bash
$ bitbake <image_name>
$ time bitbake core-image-minimal
```

#### core-image-minimal

This is a small image allowing a device to boot, and it is very useful for kernel and boot loader tests and development

#### Command to run the generated image in QEMU

Quick Emulator ( QEMU ) is a free and open source software package that performs hardware virtualization.

The QEMU based machines allow test and development without real hardware.

Currently emulations are supported for:
* ARM
* MIPS
* MIPS64
* PowerPC
* X86
* X86_64


Poky provides a script 'runqemu' which will allow you to start the QEMU using yocto generated images.

The runqemu script is run as:

```runqemu <machine> <zimage> <filesystem>```

where:

   `<machine>` is the machine/architecture to use (qemuarm/qemumips/qemuppc/qemux86/qemux86-64)\
   `<zimage>` is the path to a kernel (e.g. zimage-qemuarm.bin)\
   `<filesystem>` is the path to an ext2 image (e.g. filesystem-qemuarm.ext2) or an nfs directory

Full usage instructions can be seen by running the command with no options specified.

#### Exit QEMU

Exit QEMU by either clicking on the `shutdown icon` or by typing `Ctrl-C` in the QEMU transcript window from which you evoked QEMU.



### Steps to generate ARM image and run in QEMU
---

When you set up the build environment, a local configuration file named local.conf becomes available in a conf subdirectory of the Build Directory

The defaults are set to build for a qemux86-64 target

Edit ./build/conf/local.conf

Set ```MACHINE = "qemuarm"```
```
$ source poky/oe-init-build-env

$ bitbake core-image-minimal

$ runqemu core-image-minimal
```


#### Nographic

You can launch QEMU without the graphic window by adding nographic to the command line

```$ runqemu qemuarm nographic```


#### To add a particular package in your root file system

Open your local.conf file and add the recipe name below

```IMAGE_INSTALL += "recipe-name"```

**e.g.** \
IMAGE_INSTALL += "usbutils" for lsusb \
or \
IMAGE_INSTALL:append = " usbutils"

