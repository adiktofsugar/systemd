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

├───────────┼─────────────────────────────┼─────────────────────────────┤
│ Specifier │ Meaning                     │ Details                     │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%a"      │ Architecture                │ A short string identifying  │
│           │                             │ the architecture of the     │
│           │                             │ local system. A string such │
│           │                             │ as x86, x86-64 or arm64.    │
│           │                             │ See the architectures       │
│           │                             │ defined for                 │
│           │                             │ ConditionArchitecture=      │
│           │                             │ above for a full list.      │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%A"      │ Operating system image      │ The operating system image  │
│           │ version                     │ version identifier of the   │
│           │                             │ running system, as read     │
│           │                             │ from the IMAGE_VERSION=     │
│           │                             │ field of /etc/os-release.   │
│           │                             │ If not set, resolves to an  │
│           │                             │ empty string. See os-       │
│           │                             │ release(5) for more         │
│           │                             │ information.                │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%b"      │ Boot ID                     │ The boot ID of the running  │
│           │                             │ system, formatted as        │
│           │                             │ string. See random(4) for   │
│           │                             │ more information.           │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%B"      │ Operating system build ID   │ The operating system build  │
│           │                             │ identifier of the running   │
│           │                             │ system, as read from the    │
│           │                             │ BUILD_ID= field of          │
│           │                             │ /etc/os-release. If not     │
│           │                             │ set, resolves to an empty   │
│           │                             │ string. See os-release(5)   │
│           │                             │ for more information.       │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%C"      │ Cache directory root        │ This is either /var/cache   │
│           │                             │ (for the system manager) or │
│           │                             │ the path "$XDG_CACHE_HOME"  │
│           │                             │ resolves to (for user       │
│           │                             │ managers).                  │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%d"      │ Credentials directory       │ This is the value of the    │
│           │                             │ "$CREDENTIALS_DIRECTORY"    │
│           │                             │ environment variable if     │
│           │                             │ available. See section      │
│           │                             │ "Credentials" in            │
│           │                             │ systemd.exec(5) for more    │
│           │                             │ information.                │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%D"      │ Shared data directory       │ This is either /usr/share/  │
│           │                             │ (for the system manager) or │
│           │                             │ the path "$XDG_DATA_HOME"   │
│           │                             │ resolves to (for user       │
│           │                             │ managers).                  │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%E"      │ Configuration directory     │ This is either /etc/ (for   │
│           │ root                        │ the system manager) or the  │
│           │                             │ path "$XDG_CONFIG_HOME"     │
│           │                             │ resolves to (for user       │
│           │                             │ managers).                  │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%f"      │ Unescaped filename          │ This is either the          │
│           │                             │ unescaped instance name (if │
│           │                             │ applicable) with /          │
│           │                             │ prepended (if applicable),  │
│           │                             │ or the unescaped prefix     │
│           │                             │ name prepended with /. This │
│           │                             │ implements unescaping       │
│           │                             │ according to the rules for  │
│           │                             │ escaping absolute file      │
│           │                             │ system paths discussed      │
│           │                             │ above.                      │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%g"      │ User group                  │ This is the name of the     │
│           │                             │ group running the service   │
│           │                             │ manager instance. In case   │
│           │                             │ of the system manager this  │
│           │                             │ resolves to "root".         │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%G"      │ User GID                    │ This is the numeric GID of  │
│           │                             │ the user running the        │
│           │                             │ service manager instance.   │
│           │                             │ In case of the system       │
│           │                             │ manager this resolves to    │
│           │                             │ "0".                        │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%h"      │ User home directory         │ This is the home directory  │
│           │                             │ of the user running the     │
│           │                             │ service manager instance.   │
│           │                             │ In case of the system       │
│           │                             │ manager this resolves to    │
│           │                             │ "/root".                    │
│           │                             │                             │
│           │                             │ Note that this setting is   │
│           │                             │ not influenced by the User= │
│           │                             │ setting configurable in the │
│           │                             │ [Service] section of the    │
│           │                             │ service unit.               │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%H"      │ Host name                   │ The hostname of the running │
│           │                             │ system at the point in time │
│           │                             │ the unit configuration is   │
│           │                             │ loaded.                     │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%i"      │ Instance name               │ For instantiated units this │
│           │                             │ is the string between the   │
│           │                             │ first "@" character and the │
│           │                             │ type suffix. Empty for      │
│           │                             │ non-instantiated units.     │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%I"      │ Unescaped instance name     │ Same as "%i", but with      │
│           │                             │ escaping undone.            │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%j"      │ Final component of the      │ This is the string between  │
│           │ prefix                      │ the last "-" and the end of │
│           │                             │ the prefix name. If there   │
│           │                             │ is no "-", this is the same │
│           │                             │ as "%p".                    │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%J"      │ Unescaped final component   │ Same as "%j", but with      │
│           │ of the prefix               │ escaping undone.            │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%l"      │ Short host name             │ The hostname of the running │
│           │                             │ system at the point in time │
│           │                             │ the unit configuration is   │
│           │                             │ loaded, truncated at the    │
│           │                             │ first dot to remove any     │
│           │                             │ domain component.           │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%L"      │ Log directory root          │ This is either /var/log     │
│           │                             │ (for the system manager) or │
│           │                             │ the path $XDG_STATE_HOME    │
│           │                             │ resolves to with /log       │
│           │                             │ appended (for user          │
│           │                             │ managers).                  │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%m"      │ Machine ID                  │ The machine ID of the       │
│           │                             │ running system, formatted   │
│           │                             │ as string. See machine-     │
│           │                             │ id(5) for more information. │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%M"      │ Operating system image      │ The operating system image  │
│           │ identifier                  │ identifier of the running   │
│           │                             │ system, as read from the    │
│           │                             │ IMAGE_ID= field of          │
│           │                             │ /etc/os-release. If not     │
│           │                             │ set, resolves to an empty   │
│           │                             │ string. See os-release(5)   │
│           │                             │ for more information.       │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%n"      │ Full unit name              │                             │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%N"      │ Full unit name              │ Same as "%n", but with the  │
│           │                             │ type suffix removed.        │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%o"      │ Operating system ID         │ The operating system        │
│           │                             │ identifier of the running   │
│           │                             │ system, as read from the    │
│           │                             │ ID= field of                │
│           │                             │ /etc/os-release. See os-    │
│           │                             │ release(5) for more         │
│           │                             │ information.                │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%p"      │ Prefix name                 │ For instantiated units,     │
│           │                             │ this refers to the string   │
│           │                             │ before the first "@"        │
│           │                             │ character of the unit name. │
│           │                             │ For non-instantiated units, │
│           │                             │ same as "%N".               │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%P"      │ Unescaped prefix name       │ Same as "%p", but with      │
│           │                             │ escaping undone.            │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%q"      │ Pretty host name            │ The pretty hostname of the  │
│           │                             │ running system at the point │
│           │                             │ in time the unit            │
│           │                             │ configuration is loaded, as │
│           │                             │ read from the               │
│           │                             │ PRETTY_HOSTNAME= field of   │
│           │                             │ /etc/machine-info. If not   │
│           │                             │ set, resolves to the short  │
│           │                             │ hostname. See machine-      │
│           │                             │ info(5) for more            │
│           │                             │ information.                │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%s"      │ User shell                  │ This is the shell of the    │
│           │                             │ user running the service    │
│           │                             │ manager instance.           │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%S"      │ State directory root        │ This is either /var/lib     │
│           │                             │ (for the system manager) or │
│           │                             │ the path $XDG_STATE_HOME    │
│           │                             │ resolves to (for user       │
│           │                             │ managers).                  │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%t"      │ Runtime directory root      │ This is either /run/ (for   │
│           │                             │ the system manager) or the  │
│           │                             │ path "$XDG_RUNTIME_DIR"     │
│           │                             │ resolves to (for user       │
│           │                             │ managers).                  │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%T"      │ Directory for temporary     │ This is either /tmp or the  │
│           │ files                       │ path "$TMPDIR", "$TEMP" or  │
│           │                             │ "$TMP" are set to. (Note    │
│           │                             │ that the directory may be   │
│           │                             │ specified without a         │
│           │                             │ trailing slash.)            │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%u"      │ User name                   │ This is the name of the     │
│           │                             │ user running the service    │
│           │                             │ manager instance. In case   │
│           │                             │ of the system manager this  │
│           │                             │ resolves to "root".         │
│           │                             │                             │
│           │                             │ Note that this setting is   │
│           │                             │ not influenced by the User= │
│           │                             │ setting configurable in the │
│           │                             │ [Service] section of the    │
│           │                             │ service unit.               │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%U"      │ User UID                    │ This is the numeric UID of  │
│           │                             │ the user running the        │
│           │                             │ service manager instance.   │
│           │                             │ In case of the system       │
│           │                             │ manager this resolves to    │
│           │                             │ "0".                        │
│           │                             │                             │
│           │                             │ Note that this setting is   │
│           │                             │ not influenced by the User= │
│           │                             │ setting configurable in the │
│           │                             │ [Service] section of the    │
│           │                             │ service unit.               │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%v"      │ Kernel release              │ Identical to uname -r       │
│           │                             │ output.                     │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%V"      │ Directory for larger and    │ This is either /var/tmp or  │
│           │ persistent temporary files  │ the path "$TMPDIR", "$TEMP" │
│           │                             │ or "$TMP" are set to. (Note │
│           │                             │ that the directory may be   │
│           │                             │ specified without a         │
│           │                             │ trailing slash.)            │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%w"      │ Operating system version ID │ The operating system        │
│           │                             │ version identifier of the   │
│           │                             │ running system, as read     │
│           │                             │ from the VERSION_ID= field  │
│           │                             │ of /etc/os-release. If not  │
│           │                             │ set, resolves to an empty   │
│           │                             │ string. See os-release(5)   │
│           │                             │ for more information.       │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%W"      │ Operating system variant ID │ The operating system        │
│           │                             │ variant identifier of the   │
│           │                             │ running system, as read     │
│           │                             │ from the VARIANT_ID= field  │
│           │                             │ of /etc/os-release. If not  │
│           │                             │ set, resolves to an empty   │
│           │                             │ string. See os-release(5)   │
│           │                             │ for more information.       │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%y"      │ The path to the fragment    │ This is the path where the  │
│           │                             │ main part of the unit file  │
│           │                             │ is located. For linked unit │
│           │                             │ files, the real path        │
│           │                             │ outside of the unit search  │
│           │                             │ directories is used. For    │
│           │                             │ units that don't have a     │
│           │                             │ fragment file, this         │
│           │                             │ specifier will raise an     │
│           │                             │ error.                      │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%Y"      │ The directory of the        │ This is the directory part  │
│           │ fragment                    │ of "%y".                    │
├───────────┼─────────────────────────────┼─────────────────────────────┤
│ "%%"      │ Single percent sign         │ Use "%%" in place of "%" to │
│           │                             │ specify a single percent    │
│           │                             │ sign.                       │
├───────────┼─────────────────────────────┼─────────────────────────────┤