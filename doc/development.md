Notes for Developers
====================

If you intend to use this Docker image for building packages, you will need to uncompress
the shipped *version files* first; run once:

```bash
find /var/lib/dpkg/info -type f -name '*.lz' \
| xargs --max-args=1 --max-procs=$(nproc) --no-run-if-empty \
  plzip --keep -d
```

Use the most recent Linux Sources
---------------------------------

This applies especially if you need packages such as `libc6-dev` or `linux-libc-dev` as part
of your compilation runs, or want to use the latest vesion of **gcc**.

With *Ubuntu `bionic`* as the most recent release you will need to make available said sources
like this from their repositories:

```Dockerfile
RUN printf "deb     [arch=$(dpkg --print-architecture)] http://archive.ubuntu.com/ubuntu/ bionic main\n" >/etc/apt/sources.list.d/bionic.list \
 && printf "deb-src [arch=$(dpkg --print-architecture)] http://archive.ubuntu.com/ubuntu/ bionic main\n" >>/etc/apt/sources.list.d/bionic.list \
 && printf "Package: *\nPin: release v=18.04, l=Ubuntu\nPin-Priority: 490\n\n" >/etc/apt/preferences.d/prefer-bionic \
 && printf "Package: linux linux-*\nPin: release v=18.04, l=Ubuntu\nPin-Priority: 500\n\n" >>/etc/apt/preferences.d/prefer-bionic
```
