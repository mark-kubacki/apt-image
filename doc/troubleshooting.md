Troubleshooting
================

## Keep your Host up-to-date

For example, use Ubuntu's **Hardware Enablement [Stacks]** to get recent versions of Linux.
At some point the names has changed to **LTS Enablement Stack[s]**.
In any case, you can find more on this here:

https://wiki.ubuntu.com/Kernel/LTSEnablementStack#Kernel.2FSupport.A16.04.x_Ubuntu_Kernel_Support

```bash
# on a host running Ubuntu 16.04
$ apt-get install linux-generic-hwe-16.04
```

… and builds of the most recent kernel version here:

http://kernel.ubuntu.com/~kernel-ppa/mainline/?C=N;O=D

    Please note that starting with v4.16 you will need to install shared libraries *OpenSSL v1.1* as dependency on your host.
    Fortunately it's one *.deb file that won't affect any other package.  
    https://packages.ubuntu.com/cosmic/amd64/libssl1.1/download

## Disable Sticky Packages

Packages shouldn't be upgraded or modified between images.
If an upgrade is necessary, the image tree (or lineage, in case of Docker) should be regenerated.

To assist you with this best-practice for images, installed packages are 'sticky':
They will get assigned a higher precedence than any not installed.
In some cases you will run into something like this:

```bash
$ apt-get -s --no-install-recommends install \
  libgcc-8-dev

The following packages have unmet dependencies:
 libgcc-8-dev : Depends: libgcc1 (>= 1:8.1.0-46bn) but 1:7.3.0-8bn is to be installed

$ apt-cache policy libgcc1

libgcc1:
  Installed: 1:7.3.0-8bn
  Candidate: 1:7.3.0-8bn
  Version table:
     1:8.1.0-46bn 502
        502 https://s.blitznote.com/debs/ubuntu/amd64 staging/ Packages
     1:7.3.0-8bn 502
        502 https://s.blitznote.com/debs/ubuntu/amd64 all/ Packages
 *** 1:7.3.0-8bn 600
        600 /var/lib/dpkg/status
     1:6.0.1-0ubuntu1 500
        500 http://archive.ubuntu.com/ubuntu xenial/main amd64 Packages
```

… to which the solution is to disable said *stickyness* by deleting the file that contains corresponding preference:

```bash
$ rm /etc/apt/preferences.d/00docker
```

## Use Ubuntu's libc and/or Perl

These images come with latest versions of **libc** and **Perl**, no matter which
Ubuntu version you have downloaded.
Although this decision benefits security, you can run into packages which won't install due to dependencies.
In almost all cases it's either related to *Perl* or broken in vanilla Ubuntu, too.

To downgrade *Perl* to use packages from Ubuntu only:

```bash
. /etc/os-release
apt-get -t ${UBUNTU_CODENAME} --allow-downgrades -y install perl-base
```

If you are heavily invested in *Perl*, prefer `debase/bionic:18.04` (not `16.04`).
