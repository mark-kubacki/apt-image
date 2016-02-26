Minimal Ubuntu images for Docker
================================

Ever wondered why Ubuntu for Docker comes with *systemd* and tools for filesystem management?
Yeah, me too.
These scripts create images for Docker without that fuzz.

* blitznote/debootstrap-amd64:16.04 [![](https://badge.imagelayers.io/blitznote/debootstrap-amd64:16.04.svg)](https://imagelayers.io/?images=blitznote/debootstrap-amd64:16.04 'Get your own badge on imagelayers.io')
* blitznote/debootstrap-amd64:15.10 [![](https://badge.imagelayers.io/blitznote/debootstrap-amd64:15.10.svg)](https://imagelayers.io/?images=blitznote/debootstrap-amd64:15.10 'Get your own badge on imagelayers.io')

![size comparison Ubuntu for Docker 135MB, ubuntu-debootstrap 111MB, blitznote/debootstrap-amd64 70MB](https://raw.githubusercontent.com/Blitznote/docker-ubuntu-debootstrap/master/ubuntu-for-Docker-sizes.png)

Features
--------

* **small**: 68% the size of *ubuntu-debootstrap:15.10@56590e6e34d5* and **57% the size of ubuntu:15.10**@2804d41e7f10
* comes with *apt-transport-https*
* and latest *curl*
* a bootstrap *[ca-certificates.crt](https://github.com/wmark/docker-curl/blob/master/ca-certificates.crt)*
* latest *signify* for Linux from [Blitznote/signify](https://github.com/Blitznote/signify)
* bzip2, jq, plzip, runit (for its *chpst*), unzip
* with locale ISO.UTF-8 as default

Usage
-----

This is meant as drop-in replacement for ```FROM ubuntu``` and ```FROM ubuntu-debootstrap```.

You can use *curl* right away or start with the ```apt-get -q update``` dance as usual.
HTTPS support is already included, therefore this will work:

```Docker
FROM blitznote/debootstrap-amd64:16.04

# Just a personal preference. Contains optimized OpenSSL for SSE4:
RUN printf "deb [arch=$(dpkg --print-architecture) trusted=yes] https://s.blitznote.com/debs/ubuntu/$(dpkg --print-architecture)/ all/" \
    > /etc/apt/sources.list.d/blitznote.list \
 && printf 'Package: *\nPin: origin "s.blitznote.com"\nPin-Priority: 510\n' \
    > /etc/apt/preferences.d/prefer-blitznote

# As this image already comes with curl and jq, you could get the latest
#   release of 'something' from Github like this:
#   (The inner curl|jq gets the download location for the outer curl..|tar.)
RUN curl --silent --show-error --fail --location --header "Accept: application/tar+gzip, application/x-gzip, application/octet-stream" -o - \
    $(curl --silent --show-error --fail --location --header "Accept: application/json" \
        https://api.github.com/repos/syncthing/syncthing/releases/latest \
      | jq -r '.assets[] | select(.name | contains("linux")) | select(.name | contains("amd64")) | select(.browser_download_url | contains(".tar")) | .browser_download_url') \
    | tar -xz --no-same-owner --strip-components=1 --wildcards -C ...
```

Use lightweight *chpst* (31 kB) instead of *gosu* (2635 kB):

```diff
- gosu myuser syncthing "$@"
+ chpst -u myuser -- syncthing "$@"
- gosu nobody:root bash -c 'whoami && id'
+ chpst -u nobody:root -- bash -c 'whoami && id'
```

```bash
# from gosu to chpst:
sed -e 's:gosu \([^ ]*\) :chpst -u \1 -- :g'
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

The images for architecture *amd64*/x86_64 require instruction set **SSE 4**, which had been introduced in 2007.
Not having a reasonably recent CPU will trigger the ```illegal instruction``` error.
