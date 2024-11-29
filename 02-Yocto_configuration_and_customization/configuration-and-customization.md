
# Configuration and customization

## configuration files
- local.conf
- bblayer.conf

lets go through one by one -

### 1. local.conf
This file is used to **customize the image** and **add packages**

### Default Configurations in local.conf file
```bash
MACHINE ??= "qemux86-64"
DL_DIR, SSTATE_DIR, TMPDIR
DISTRO ?= "poky"
PACKAGE_CLASSES ?= "package_rpm"
EXTRA_IMAGE_FEATURES ?= "debug-tweaks"
USER_CLASSES ?= "buildstats"
PATCHRESOLVE = "noop"
BB_DISKMON_DIRS
CONF_VERSION = "2"
```

### configurations in detail

| Configuration | Description   |
| ------------- | ------------- |
| **MACHINE**   | Specifies the target device for which the image is built. <br> default MACHINE conf File locations **poky/meta/conf/machines**  |
| **DL_DIR**    | specifies the directory where source code archives for packages will be downloaded|
| **SSTATE_DIR**   | specifies the directory where shared state cache files are stored.|
| **TMPDIR**  | specifies the directory to be used for temporary files by various programs and scripts|
|**DISTRO** | specifies the name of the distribution that is being built. <br>    Setting  **DISTRO**  to  **"poky"**  means that the build system will use the configuration files and package recipes that are part of the reference distribution.|
|**PACKAGE_CLASSES**| specifies the types of packages to be created for the target system.|
|**EXTRA_IMAGE_FEATURES**| specifies additional features to be included in the target image|
|**USER_CLASSES**|The  **buildstats**  class records performance statistics about each task executed during the build (e.g. elapsed time, CPU usage, and I/O usage).|
|**PATCHRESOLVE**|Determines the action to take when a patch fails. You can set this variable to one of two values:  **noop**  and  **user**. <br> The default value of  **noop**  causes the build to simply fail when the build system cannot successfully apply a patch. <br> Setting the value to  **user**  causes the build system to launch a shell and places you in the right location so that you can manually resolve the conflicts.|
|**BB_DISKMON_DIRS**|Monitors disk space and available inodes during the build|
|**CONF_VERSION**| specify the version of the configuration syntax to use. <br> setting **CONF_VERSION = "2"** in `local.conf` enables the use of newer features and syntax in Yocto|


### Additional config for local.conf
In order to reduce disk usage, we can automatically remove the work directory after each recipe compilation cycle, adding
```bash
INHERIT += "rm_work"
RM_WORK_EXCLUDE += "linux-yocto u-boot"
```
By default, images are not erased from the deploy directory, but you can configure your
project to remove the previously built version of the same image by adding the following
```bash
RM_OLD_IMAGE = "1"
```
Keep threads twice host CPU cores
```bash
BB_NUMBER_THREADS ?= "${@oe.utils.cpu_count()}"

PARALLEL_MAKE ?= "-j ${@oe.utils.cpu_count()}"
```
Disable nw usage if previously downloaded data
```bash
BB_NO_NETWORK = "1"
```
speed-up the build
```bash
BB_GENERATE_MIRROR_TARBALLS = "1"
SOURCE_MIRROR_URL ?= "file:///home/you/your-download-dir/"
INHERIT += "own-mirrors"
```
### 2. bblayer.conf
In this file the set of layers are defined that should be included in a build.

#### what is Layer ?
A collection of related recipes or Layers are recipe containers (folders)
Typical naming convention: **meta-\<layername>**

Poky has the following layers:
> meta, meta-poky, meta-selftest, meta-skeleton, meta-yocto-bsp

#### why Layers ?
Layers provide a mechanism to isolate meta data according to functionality, for instance BSPs, distribution configuration, etc.

You could have a BSP layer, a GUI layer, a distro configuration, middleware, or an application

Putting your entire build into one layer limits and complicates future customization and reuse. 

Example: 
meta-poky          -- Distro metadata
meta-yocto-bsp     -- BSP    metadata

Layers allow to easily to add entire sets of meta data and/or replace sets with other sets.
meta-poky, is itself a layer applied on top of the OE-Core metadata layer, meta

`BBLAYERS` variable present in build/conf/bblayers.conf file list the layers Bitbake tries to find

If bblayers.conf is not present when you start the build, the OpenEmbedded build system creates it from bblayers.conf.sample when you source the oe-init-build-env script

