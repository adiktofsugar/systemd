# systemd scripts
systemd is complicated, but can also do everything and is used in a bunch of places.

This repo is ideally going to contain many systemd units (services / sockets / etc) that I use for common enough things.

I'd like to be able to make this a public repo so hopefully it won't need to have a bunch of me-only stuff in it.

# How to install
systemd works in either global or user mode, but defaults to global, which needs sudo. These systemd units are designed to be user facing, so we'll be using `systemctl --user`
```
systemctl --user enable --now path/to/file
```
- `enable` creates a set of symlinks and reloads the system manager (daemon-reload). it doesn't start anything unless you use --now. it should create a symlink to the appropriate systemd directory (`~/.config/systemd/user` for --user).

## Check status
```
systemctl --user status service_name
```
"service_name" is the name of the file (not the path). For example `gdrive.service`.