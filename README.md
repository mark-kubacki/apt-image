Minimal Ubuntu images for Docker
================================

Ever wondered why Ubuntu for Docker comes with *systemd* and tools for filesystem management?
Yeah, me too.
These scripts create images for Docker without that fuzz.

* blitznote/debootstrap-amd64:16.04 [![](https://badge.imagelayers.io/blitznote/debootstrap-amd64:16.04.svg)](https://imagelayers.io/?images=blitznote/debootstrap-amd64:16.04 'Get your own badge on imagelayers.io')
* blitznote/debootstrap-amd64:15.10 [![](https://badge.imagelayers.io/blitznote/debootstrap-amd64:15.10.svg)](https://imagelayers.io/?images=blitznote/debootstrap-amd64:15.10 'Get your own badge on imagelayers.io')

![size comparison: Ubuntu for Docker 120MB, ubuntu-debootstrap 87MB, blitznote/debootstrap-amd64 55MB](https://rawgit.com/Blitznote/docker-ubuntu-debootstrap/master/ubuntu-for-Docker-sizes.svg)

Features
--------

* **small**:
  * 63% the size of ubuntu-debootstrap (:16.04@898cb62b7368)
  * 45% the size of ubuntu (:16.04@44776f55294a)
* comes with *apt-transport-https*
* and latest *curl*
* a bootstrap *[ca-certificates.crt](https://github.com/wmark/docker-curl/blob/master/ca-certificates.crt)*
* latest *signify* for Linux from [Blitznote/signify](https://github.com/Blitznote/signify)
* bzip2, jq, plzip, runit (for its *chpst*), unzip
* with locale ISO.UTF-8 as default

Usage
-----

This is meant as drop-in replacement for ```FROM ubuntu``` and ```FROM ubuntu-debootstrap```.

You can use *curl* right away or start with ```apt-get -q update``` as usual.
HTTPS support is already included in *apt*!

Use lightweight *chpst* (31 kB) instead of *gosu* (2635 kB):

```diff
- gosu myuser syncthing "$@"
+ chpst -u myuser -- syncthing "$@"
- gosu nobody:root bash -c 'whoami && id'
+ chpst -u nobody:root -- bash -c 'whoami && id'
```

To account for differences between *gpg v1* and *gpg v2*
I've created a script for fetching keys from keyservers:

```bash
/usr/bin/get-gpg-key 0xcbcb082a1bb943db 0xa6a19b38d3d831ef \
| apt-key add
```

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

Images for architecture *amd64*/x86_64 require instruction set **SSE 4**, which had been introduced in 2007.
Not having a reasonably recent CPU will trigger the ```illegal instruction``` error.
