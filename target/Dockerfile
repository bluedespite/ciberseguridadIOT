FROM debian:bookworm-slim

RUN apt-get update \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      openssh-server \
 && rm -rf /var/lib/apt/lists/* \
 && mkdir -p /run/sshd \
 && useradd --create-home --shell /bin/bash iotlab \
 && echo 'iotlab:class-demo-2026' | chpasswd \
 && printf '\nPasswordAuthentication yes\nKbdInteractiveAuthentication no\nPermitRootLogin no\nUsePAM no\nLogLevel VERBOSE\n' >> /etc/ssh/sshd_config

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D", "-e"]
