FROM nvidia/cuda:12.5.0-runtime-ubuntu22.04

RUN apt-get update && \
    apt-get install -y bash git openssh-server rsync augeas-tools xz-utils curl gpg locales locales-all && \
    deluser $(getent passwd 33 | cut -d: -f1) && \
    delgroup $(getent group 33 | cut -d: -f1) 2>/dev/null || true && \
    mkdir -p ~root/.ssh /etc/authorized_keys && chmod 700 ~root/.ssh/ && \
    augtool 'set /files/etc/ssh/sshd_config/AuthorizedKeysFile ".ssh/authorized_keys /etc/authorized_keys/%u"' && \
    printf "Port 22\n" >> /etc/ssh/sshd_config && \
    cp -a /etc/ssh /etc/ssh.cache && \
    rm -rf /var/lib/apt/lists/*

ENV LANG C.UTF-8
#ENV LANGUAGE en_US:en
#ENV LC_ALL en_US.UTF-8

#Install Jellyfin-ffmpeg7
#https://jellyfin.org/docs/general/administration/hardware-acceleration/nvidia/#configure-with-linux-virtualization
RUN apt-get update &&  \
    apt-get install -y software-properties-common && \
    add-apt-repository -y universe && \
    mkdir -p /etc/apt/keyrings && \
    curl -fsSL https://repo.jellyfin.org/jellyfin_team.gpg.key | gpg --dearmor -o /etc/apt/keyrings/jellyfin.gpg && \
    export VERSION_OS="$( awk -F'=' '/^ID=/{ print $NF }' /etc/os-release )" && \
    export VERSION_CODENAME="$( awk -F'=' '/^VERSION_CODENAME=/{ print $NF }' /etc/os-release )" && \
    export DPKG_ARCHITECTURE="$( dpkg --print-architecture )" && \
cat <<EOF | tee /etc/apt/sources.list.d/jellyfin.sources
Types: deb 
URIs: https://repo.jellyfin.org/${VERSION_OS} 
Suites: ${VERSION_CODENAME} 
Components: main 
Architectures: ${DPKG_ARCHITECTURE} 
Signed-By: /etc/apt/keyrings/jellyfin.gpg 
EOF
RUN apt update && \
    apt install -y jellyfin-ffmpeg7 


#Alternate way
#  RUN wget -P /tmp https://github.com/jellyfin/jellyfin-ffmpeg/releases/download/v6.0.1-7/jellyfin-ffmpeg_6.0.1-7_portable_linux64-gpl.tar.xz
#  RUN tar -xf /tmp/jellyfin-ffmpeg_6.0.1-7_portable_linux64-gpl.tar.xz -C /usr/bin  && rm /tmp/jellyfin-ffmpeg_6.0.1-7_portable_linux64-gpl.tar.xz


EXPOSE 22

COPY entry.sh /entry.sh

ENTRYPOINT ["/entry.sh"]

CMD ["/usr/sbin/sshd", "-D", "-e", "-f", "/etc/ssh/sshd_config"]
