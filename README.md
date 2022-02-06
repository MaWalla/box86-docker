# box86-docker

First of all, huge thanks to [anujdatar/box86-docker](https://github.com/anujdatar/box86-docker) for providing the base of this setup and [ptitSeb/box86](https://github.com/ptitSeb/box86) for providing the software and guide for wine.

## Installation
To use this setup, you'll need docker. The package name may vary by distribution.

Arch Linux and Arch based distros like Manjaro use `sudo pacman -S docker`.
You'll also have to enable and start the systemd service by running `sudo systemctl enable --now docker.service`

On Mobian you'd use `sudo apt install docker.io`.
It should enable the service automatically itself. If you get problems with the docker socket, you may have to enable it like for Arch above.

## Usage
I provide 2 images, ready for use.
  - [mawalla/box86](https://hub.docker.com/repository/docker/mawalla/box86)

  - [mawalla/box86-wine](https://hub.docker.com/repository/docker/mawalla/box86-wine)

Those can be extended to fit your own needs. The most simple way of running is `docker run -it mawalla/box86 bash`. This drops you into a shell where you can use `box86`. You can also skip the -it flags and run a command directly.

Keep in mind that you'll either have to use sudo to run the container, or add your user to the docker group. That may not exist yet, so you may have to run `sudo groupadd docker` first.

If you go a bit further and want to run GUI applications like wine games, some env vars need to be set and paths mapped. The following command could be used for example. Note that the mappings for .wine and Games are optional, but recommended to keep the wine prefix persistent and actually get games in there. The path before the colon is on your device and the path behind is inside the container.
`docker run -it -e DISPLAY=$DISPLAY -v ${HOME}/.Xauthority:/home/box86/.Xauthority -v /tmp/.X11-unix:/tmp/.X11-unix -v ${HOME}/.wine:/home/box86/.wine -v ${HOME}/Games:/home/box86/Games -v /var/run/dbus:/var/run/dbus --device /dev/dri:/dev/dri --device /dev/snd:/dev/snd mawalla/box86-wine bash`

## Issues
  - This setup doesn't include mappings for either Pulseaudio or Wayland yet, so sound and apps needing Wayland for display won't work with only that. I'll add those later.

  - Some applications that *should* work according to the [box86 compatibility list](https://box86.org/app/), just don't... I don't know if dependencies, settings or outdated mesa may be at fault though.

## Using with other CPUs
This may already work with my provided images, I don't know. If not, getting them to work should be as simple as changing the compile flags in box86-docker/Dockerfile to something fitting from [here](https://ptitseb.github.io/box86/COMPILE.html).

## Building yourself

Change into the box86-docker directory and run `docker buildx build --platform linux/armhf -t box86 .`

If you wanna build it on a computer with x86 CPU, you'll have to setup QEMU first. Refer to the [Arch Wiki](https://wiki.archlinux.org/title/QEMU#Chrooting_into_arm/arm64_environment_from_x86_64) article for more information. Everything until, and including the `systemctl restart` line will be relevant, packages should be similar for other distributions.

The same can be done with wine, by changing into the box86-wine-docker directory and running `docker buildx build --platform linux/armhf -t box86-wine .`

You may wanna adjust the FROM line in the Dockerfile there, so it uses the box86 image built earlier.

## Todo
  - compiling mesa myself, because 20.3 provided with Debian 11 is already fairly old and newer versions may yield performance improvements.

  - write a python build script to automatically fetch the latest wine release for automated builds
