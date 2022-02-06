FROM mawalla/box86

# switch back to root, since the following steps will need that.
USER root

# install wget to download wine and winetricks
RUN apt-get update && apt-get install -y wget && rm -rf /var/lib/apt/lists/*

# download and extract wine
WORKDIR /wine-setup
RUN wget https://dl.winehq.org/wine-builds/debian/dists/bullseye/main/binary-i386/wine-staging_7.1~bullseye-1_i386.deb && \
    wget https://dl.winehq.org/wine-builds/debian/dists/bullseye/main/binary-i386/wine-staging-i386_7.1~bullseye-1_i386.deb && \
    dpkg-deb -xv wine-staging_7.1~bullseye-1_i386.deb wine-installer && \
    dpkg-deb -xv wine-staging-i386_7.1~bullseye-1_i386.deb wine-installer && \
    mv /wine-setup/wine-installer/opt/wine* /wine && \
    rm -rf /wine-setup
WORKDIR /home/box86

# add some links, so that wine is in PATH
RUN ln -s /wine/bin/wine /usr/local/bin/wine && \
    ln -s /wine/bin/wineboot /usr/local/bin/wineboot && \
    ln -s /wine/bin/winecfg /usr/local/bin/winecfg && \
    ln -s /wine/bin/wineserver /usr/local/bin/wineserver && \
    chmod +x /usr/local/bin/wine /usr/local/bin/wineboot /usr/local/bin/winecfg /usr/local/bin/wineserver

# install winetricks (which currently doesn't work for some reason though)
RUN wget https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks && \
    chmod +x winetricks && \
    mv winetricks /usr/local/bin/

# install dependencies to make applications work
RUN apt-get update && apt-get install -y cabextract libstb0 libfreetype6 fontconfig xorg libasound2 libpulse0 && \
    rm -rf /var/lib/apt/lists/*

# finally switch back to the box86 user so permissions work out
USER box86
