FROM scratch
LABEL org.label-schema.vendor="Blitznote" \
      org.label-schema.build-date="2017-11-03T23:20:00+00:00" \
      org.label-schema.name="minimal base images with Ubuntu" \
      org.label-schema.vcs-type="git" \
      org.label-schema.vcs-url="https://github.com/Blitznote/debase" \
      org.label-schema.schema-version="1.0rc1" \
      operatingsystem="linux" \
      kernelversion="3.18.0" \
      minkernelversion="3.16.0" \
      arch="amd64" \
      subarch="cx16 fxsr pclmul popcnt rdrand sahf mmx sse2 sse3 ssse3 sse4_1 sse4_2" \
      family="ivybridge silvermont excavator zx-c" \
      minfamily="sandybridge silvermont piledriver zx-c"

ADD rootfs.tar /
CMD ["/bin/bash"]
SHELL ["/bin/bash", "-c"]
