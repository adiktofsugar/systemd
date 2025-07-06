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

# SPECIFIERS

Special `%` prefixed tokens described in the SPECIFIERS section of `man systemd.unit`

| Specifier | Meaning                        | Details                                                                                                                                |
| --------- | ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| `%a`      | Architecture                   | A short string identifying the architecture of the local system, such as x86, x86-64, or arm64. See `ConditionArchitecture=` for more. |
| `%A`      | Operating system image version | Identifier read from `IMAGE_VERSION=` in `/etc/os-release`. May be empty. See `os-release(5)`.                                         |
| `%b`      | Boot ID                        | The boot ID of the running system. See `random(4)`.                                                                                    |
| `%B`      | Operating system build ID      | Identifier from `BUILD_ID=` in `/etc/os-release`. May be empty. See `os-release(5)`.                                                   |
| `%C`      | Cache directory root           | `/var/cache` for system manager, or `$XDG_CACHE_HOME` for user managers.                                                               |
| `%d`      | Credentials directory          | From `$CREDENTIALS_DIRECTORY` env var. See "Credentials" in `systemd.exec(5)`.                                                         |
| `%D`      | Shared data directory          | `/usr/share/` or `$XDG_DATA_HOME` depending on manager type.                                                                           |
| `%E`      | Configuration directory root   | `/etc/` or `$XDG_CONFIG_HOME` depending on manager type.                                                                               |
| `%f`      | Unescaped filename             | The unescaped instance or prefix name with `/` prepended.                                                                              |
| `%g`      | User group                     | Name of the group running the service manager. `"root"` for system manager.                                                            |
| `%G`      | User GID                       | Numeric GID of the user. `"0"` for system manager.                                                                                     |
| `%h`      | User home directory            | User\u2019s home dir, or `/root` for system manager. Not affected by `User=` in unit file.                                             |
| `%H`      | Host name                      | Hostname at the time unit config is loaded.                                                                                            |
| `%i`      | Instance name                  | Part between `@` and suffix in instantiated units. Empty if not instantiated.                                                          |
| `%I`      | Unescaped instance name        | Like `%i`, but with escaping undone.                                                                                                   |
| `%j`      | Final component of prefix      | Portion after last `-` in the prefix name. Same as `%p` if no `-`.                                                                     |
| `%J`      | Unescaped final component      | Like `%j`, but with escaping undone.                                                                                                   |
| `%l`      | Short host name                | Hostname truncated at first `.` to remove domain part.                                                                                 |
| `%L`      | Log directory root             | `/var/log` or `$XDG_STATE_HOME/log` depending on manager type.                                                                         |
| `%m`      | Machine ID                     | The machine ID string. See `machine-id(5)`.                                                                                            |
| `%M`      | Operating system image ID      | From `IMAGE_ID=` in `/etc/os-release`. May be empty. See `os-release(5)`.                                                              |
| `%n`      | Full unit name                 | (no details provided in original)                                                                                                      |
| `%N`      | Full unit name (no suffix)     | Like `%n`, but with the type suffix removed.                                                                                           |
| `%o`      | Operating system ID            | From `ID=` in `/etc/os-release`. See `os-release(5)`.                                                                                  |
| `%p`      | Prefix name                    | Part before `@` in instantiated units. Same as `%N` otherwise.                                                                         |
| `%P`      | Unescaped prefix name          | Same as `%p`, but with escaping undone.                                                                                                |
| `%q`      | Control group path             | The control group path of the unit, formatted as string.                                                                               |
| `%r`      | Runtime directory root         | This is either /run (for the system manager) or the path `$XDG_RUNTIME_DIR` resolves to (for user managers).                           |
| `%R`      | Unit runtime directory         | This is the unit-specific runtime directory, below the runtime directory root.                                                         |
| `%s`      | Slice name                     | The slice unit name the unit is placed in.                                                                                             |
| `%S`      | Unescaped slice name           | Same as `%s`, but with escaping undone.                                                                                                |
| `%t`      | System runtime directory       | This is the value of the RuntimeDirectory= setting in the service unit.                                                                |
| `%T`      | Temporary directory            | This is either /tmp (for the system manager) or the path `$XDG_RUNTIME_DIR/tmp` resolves to (for user managers).                       |
| `%u`      | User name                      | The name of the user running the service manager instance.                                                                             |
| `%U`      | UID                            | The numeric UID of the user running the service manager instance.                                                                      |
| `%v`      | Kernel release                 | The kernel release string, as returned by uname(2).                                                                                    |
| `%V`      | Kernel version                 | The kernel version string, as returned by uname(2).                                                                                    |
| `%w`      | Working directory              | The working directory of the service.                                                                                                  |
| `%W`      | Watchdog timeout               | The watchdog timeout value in microseconds.                                                                                            |
| `%x`      | Executable path                | The absolute path to the unit’s executable binary.                                                                                     |
| `%X`      | Timestamp in UTC               | A timestamp formatted as YYYY-MM-DD HH:MM:SS in UTC.                                                                                   |
| `%y`      | Timestamp in local time        | A timestamp formatted as YYYY-MM-DD HH:MM:SS in local time.                                                                            |
| `%Y`      | Year                           | The current year in 4-digit format.                                                                                                    |
| `%z`      | Timezone offset                | The timezone offset from UTC, formatted as ±HHMM.                                                                                      |
| `%Z`      | Timezone name                  | The name of the timezone.                                                                                                              |

# Timers and services
Timers by default start a service of the same name, so foo.timer is related to foo.service. This can replace cron. When setting up a system like this, you normally set the service to a oneshot service and make sure the timer is installed (at default.target or some multi-user.target or something).

## Time units

- usec, us, μs
- msec, ms
- seconds, second, sec, s
- minutes, minute, min, m
- hours, hour, hr, h
- days, day, d
- weeks, week, w
- months, month, M (defined as 30.44 days)
- years, year, y (defined as 365.25 days)