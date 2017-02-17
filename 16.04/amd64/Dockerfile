FROM scratch
LABEL org.label-schema.vendor="Blitznote" \
      org.label-schema.build-date="2017-02-17T17:30:00+00:00" \
      org.label-schema.name="minimal base images with Ubuntu" \
      org.label-schema.vcs-type="git" \
      org.label-schema.vcs-url="https://github.com/Blitznote/docker-ubuntu-debootstrap"

ADD rootfs.tar /
CMD ["/bin/bash"]
SHELL ["/bin/bash", "-c"]
