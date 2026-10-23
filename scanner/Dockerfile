FROM debian:bookworm-slim

RUN apt-get update \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      nmap ca-certificates \
 && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /lab \
 && printf 'iotlab\n' > /lab/users.txt \
 && printf 'not-the-password\nwrong-example\nclass-demo-2026\n' > /lab/passwords.txt

WORKDIR /lab
CMD ["bash"]
