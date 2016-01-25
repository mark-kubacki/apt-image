Minimal Ubuntu images for Docker
================================

Ever wondered why Ubuntu for Docker comes with *systemd* and tools for filesystem management?
Yeah, me too.
These scripts create images without that fuzz.

* blitznote/debootstrap-amd64:16.04 [![](https://badge.imagelayers.io/blitznote/debootstrap-amd64:16.04.svg)](https://imagelayers.io/?images=blitznote/debootstrap-amd64:16.04 'Get your own badge on imagelayers.io')
* blitznote/debootstrap-amd64:15.10 [![](https://badge.imagelayers.io/blitznote/debootstrap-amd64:15.10.svg)](https://imagelayers.io/?images=blitznote/debootstrap-amd64:15.10 'Get your own badge on imagelayers.io')

Features
--------

* no *init* systems and tools for filesystem management to save space and bandwidth
* **small**: *15.10@ae4ae7d45e72* is 68% the size of *ubuntu-debootstrap:15.10@56590e6e34d5* and **57% the size of ubuntu:15.10**@2804d41e7f10
* yet it comes with *apt-transport-https*
* and latest *cURL*
* a bootstrap *[ca-certificates.crt](https://github.com/wmark/docker-curl/blob/master/ca-certificates.crt)*
* latest *signify* for Linux from [Blitznote/signify](https://github.com/Blitznote/signify)
* an UTF-8 locale as default

Usage
-----

This is meant as drop-in replacement for ```FROM ubuntu``` and ```FROM ubuntu-debootstrap```.
Start your *Dockerfile* by:

```Docker
FROM		blitznote/debootstrap-amd64:16.04
```

You can use *curl* right away or start with the ```apt-get -q update``` dance as usual.
HTTPS support is already included.

Regenerate the Images
---------------------

```bash
apt-get -y install multistrap docker-engine curl

ARCH=amd64 \
DOCKER_PREFIX=blitznote/ \
./build.sh 15.10 16.04
```

Caveats
-------

The images for architecture *amd64*/x86_64 require instruction set **SSE 4**, which had been introduced in 2007.
Not having a reasonably recent CPU will trigger a ```illegal instruction``` error.
