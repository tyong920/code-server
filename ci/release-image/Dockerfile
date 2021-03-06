FROM debian:10

RUN apt-get update
RUN apt-get install -y curl

# https://wiki.debian.org/Locale#Manually
RUN apt-get install -y locales
RUN sed -i "s/# en_US.UTF-8/en_US.UTF-8/" /etc/locale.gen
RUN locale-gen
ENV LANG=en_US.UTF-8

RUN chsh -s /bin/bash
ENV SHELL=/bin/bash

RUN apt-get install -y dumb-init sudo
RUN apt-get install -y man procps vim nano htop ssh git

RUN adduser --gecos '' --disabled-password coder && \
  echo "coder ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/nopasswd

RUN curl -SsL https://github.com/boxboat/fixuid/releases/download/v0.4/fixuid-0.4-linux-amd64.tar.gz | tar -C /usr/local/bin -xzf - && \
    chown root:root /usr/local/bin/fixuid && \
    chmod 4755 /usr/local/bin/fixuid && \
    mkdir -p /etc/fixuid && \
    printf "user: coder\ngroup: coder\n" > /etc/fixuid/config.yml

RUN rm -rf /var/lib/apt/lists/*

COPY release/code-server*.tar.gz /tmp
RUN cd /tmp && tar -xzf code-server*.tar.gz && rm code-server*.tar.gz && \
  mv code-server* /usr/local/lib/code-server && \
  sed 's/\$0/\/usr\/local\/lib\/code-server\/code-server/g' /usr/local/lib/code-server/code-server > /usr/local/bin/code-server && \
  chmod +x /usr/local/bin/code-server

EXPOSE 8080
USER coder
WORKDIR /home/coder
ENTRYPOINT ["dumb-init", "fixuid", "-q", "/usr/local/bin/code-server", "--host", "0.0.0.0", "."]
