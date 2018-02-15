# Singularity VNC Server

[![Singularity Hub](https://www.singularity-hub.org/static/img/hosted-singularity--hub-%23e32929.svg)](https://singularity-hub.org/collections/603)
[![GitHub License](https://img.shields.io/badge/license-MIT-green.svg)](https://opensource.org/licenses/MIT)

Singularity image for [TurboVNC] with the inclusion of [websockify] for
connecting to the VNC server from within your browser using [noVNC].

This is still a work in progress.

## Build

You can build a local Singularity image named `singularity-vncserver.simg`
with:

```sh
sudo singularity build singularity-vncserver.simg Singularity
```

## Deploy

Instead of building it yourself you can download the pre-built image from
[Singularity Hub](https://www.singularity-hub.org) with:

```sh
singularity pull --name singularity-vncserver.simg shub://nickjer/singularity-vncserver
```

## Run

### vncserver

The `vncserver` command is launched using the default run command:

```sh
singularity run singularity-vncserver.simg
```

or as an explicit app:

```sh
singularity run --app vncserver singularity-vncserver.simg
```

Example:

```console
$ singularity run --app vncserver singularity-vncserver.simg

You will require a password to access your desktops.

Password:
Verify:
Would you like to enter a view-only password (y/n)? n

Desktop 'TurboVNC: dev:1 (nickjer)' started on display dev:1

Creating default startup script /home/nickjer/.vnc/xstartup.turbovnc
Starting applications specified in /home/nickjer/.vnc/xstartup.turbovnc
Log file is /home/nickjer/.vnc/dev:1.log

$ singularity run --app vncserver singularity-vncserver.simg -kill :1
Killing Xvnc process ID 9738
```

### vncpasswd

The `vncpasswd` command is launched as an explicit app:

```sh
singularity run --app vncpasswd singularity-vncserver.simg
```

Example:

```console
$ echo "mypassword" | singularity run --app vncpasswd singularity-vncserver.simg -f > vnc_passwd
Warning: password truncated to the length of 8.
```

### websockify

In some cases you may not want to download and install a VNC client on your
local machine. In those cases you can actually use the [noVNC] client which
runs completely in your browser.

In order to connect to the VNC server with [noVNC] you will need to enable
[websockify] which will translate the incoming websocket traffic from [noVNC]
to normal TCP traffic proxied to the listening VNC server.

The `websockify` command is launched as an explicit app:

```sh
singularity run --app websockify singularity-vncserver.simg
```

Assuming you started a `vncserver` above listening on port `5901` (display port
`:1`), you will launch `websockify` on the same machine with:

```console
$ singularity run --app websockify singularity-vncserver.simg 8000 localhost:5901
WebSocket server settings:
  - Listen on :8000
  - No SSL/TLS support (no cert file)
  - proxying from :8000 to localhost:5901
```

Then from your browser using the [noVNC] client, connect to the machine running
the VNC server and port `8000`.

**It is recommended you either setup SSL for a secure connection or host it
from behind a reverse proxy with SSL already enabled.**

## Contributing

Bug reports and pull requests are welcome on GitHub at
https://github.com/nickjer/singularity-vncserver.

## License

The code is available as open source under the terms of the [MIT License].

[TurboVNC]: https://turbovnc.org/
[websockify]: https://github.com/novnc/websockify/
[noVNC]: https://kanaka.github.io/noVNC/
[MIT License]: http://opensource.org/licenses/MIT
