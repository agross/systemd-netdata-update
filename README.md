# systemd-netdata-update

Automatic [netdata](https://github.com/firehol/netdata) update
[as described in the Wiki](https://github.com/firehol/netdata/wiki/Installation#updating-netdata-after-its-installation),
but triggered an monitored by systemd.

If netdata configuration files are under git with e.g.
[etckeeper](https://github.com/joeyh/etckeeper)
changes to these files will be auto-committed after updates.

## Installation

1. Decide where you want to install things.

   ```sh
   INSTALL_DIR=/usr/local/src/systemd-netdata-update
   ```

1. Clone this repository.

   ```sh
   $ git clone https://github.com/agross/systemd-netdata-update.git "$INSTALL_DIR"
   Cloning into 'systemd-netdata-update'...
   ```

1. Install the systemd unit.

   ```sh
   $ "$INSTALL_DIR/install"
   Linking /usr/local/src/systemd-netdata-update/netdata-update.service
   Created symlink /etc/systemd/system/netdata-update.service → /usr/local/src/systemd-netdata-update/netdata-update.service.
   Linking /usr/local/src/systemd-netdata-update/netdata-update.timer
   Created symlink /etc/systemd/system/netdata-update.timer → /usr/local/src/systemd-netdata-update/netdata-update.timer.
   Enabling /usr/local/src/systemd-netdata-update/netdata-update.timer
   Created symlink /etc/systemd/system/timers.target.wants/netdata-update.timer → /usr/local/src/systemd-netdata-update/netdata-update.timer.
   ```

1. Tell `netdata-update.service` where you installed netdata.

   ```sh
   $ systemctl edit netdata-update.service

   # Editor opens. Add these lines and save the file.

   [Service]
   ExecStart=/full/path/to/netdata-updater.sh
   ```

## Optional configuration items to think about

### Get notified when `netdata-update.service` fails

You can tell systemd to start another unit whenever a unit fails. See
[this blog post](http://northernlightlabs.se/systemd.status.mail.on.unit.failure)
for how it is done. Also see my
[systemd-unit-status-mail](https://github.com/agross/systemd-unit-status-mail)
repository that contains everything you need.

After `unit-status-mail` is in place, add it to your `netdata-update.service` using a
systemd override:

```sh
$ systemctl edit netdata-update.service

# Editor opens. Add these lines and save the file.

[Unit]
OnFailure=unit-status-mail@%n.service
```

### Test your setup

```sh
$ systemctl start netdata-update.service
$ journalctl --follow --unit netdata-update.service

# Watch logs being written.
```

### Getting the whole picture

To get all `netdata-update.service` and `netdata-update.timer` settings use

* `systemctl cat netdata-update.service` or `systemctl cat netdata-update.timer` and
* `systemctl show netdata-update.service` or `systemctl show netdata-update.timer`.
