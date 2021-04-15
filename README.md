# Display Brightness Slider for Gnome Shell

## Brightness Control

1. install `ddcutil`

2. Manually load kernel module `i2c-dev`

```

modprobe i2c-dev

```

3. Verify that your monitor supports brightness control

```

ddcutil capabilities | grep "Feature: 10"

```

4. udev rule for giving group i2c RW permission on the `/dev/i2c` devices

```

sudo cp /usr/share/ddcutil/data/45-ddcutil-i2c.rules /etc/udev/rules.d

```

5. Create i2c group and add yourself

  ```

  sudo usermod your-user-name -aG i2c

  sudo groupadd --system i2c

  ```

6. load `i2c-dev` automatically

```

touch /etc/modules-load.d/i2c.conf

echo "i2c-dev" >> /etc/modules-load.d/i2c.conf

```

This tool uses ddcutil as backend, so first make sure that your user can use use following shell commands without root or sudo.

`ddcutil getvcp 10` to check the brightness of a monitor and

`ddcutil setvcp 10 100` to set the brightness to 100

It automatically supports multiple displays detected by

`ddcutil detect`

## Issues

### Screen hangs/locks on first startup
In my hardware for some reason when `ddcutil detect` is ran for the first time after a cold boot and then, when it checks for i2c busno=1, whole system locks for couple of seconds.
As a workaround I changed this extension to read cached info from a file, when it exists.
```
ddcutil --brief detect > $XDG_CACHE_HOME/ddcutil_detect
```

