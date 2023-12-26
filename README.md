# oci-media-server
A collection of configurations for my home media server

This setup uses OpenSUSE MicroOS as the base server OS.
That is because it is an 'immutable' minimal Linux Distro which is incredibly stable and receives daily updates to the base level OS packages.

The basic design for this OS is that most services run isolated to the host OS. So there is a layer of security associated with this system where if one of the services is compromised it doesn't affect the rest of the system.

Because of that, the system is setup to take advantage of running its server services in rootless podman containers. These containers do not have access to change fundamental parts of the server. Podman is a tool similar to docker for running oci containers, however it can run and operate daemon-less.

### Setup

This setup makes heavy use of systemd services/containers running for a non-root user.

1. Make a non-root user.
2. Allow systemd services to run on boot for this user. The command for that is `loginctl enable-linger <username>`
3. Create and place all systemd container files in `~/.config/containers/systemd`
4. Start the containers with `systemd --user start <container>`

### Other things to note

This configuration maps the container config files to /var/srv/. So you'll need to create those service folders in that location for each service and make them accessible/owned by the user running the containers.

Likewise, the same goes for the location of the media. I map those volumes to /var/iron/Media, however you can choose any location you want mounted for your media.

All of the services should restart when the computer reboots and everything should be working out of the box. The containers even auto update themselves when using the autoupdate systemd service as well, which is included in MicroOS. It is called the podman-auto-update.timer.

For the wireguard setup, you need to have the client config file located in the /var/srv/wireguard/wg_confs/ folder. The service will automatically bring up any tunnels placed in there.

### Debugging

1. `podman logs -f wireguard` -- See the logs for the container having issues.
2. `sudo ausearch -m AVC,USER_AVC -ts recent` -- See the logs for selinux, which commonly blocks running services.