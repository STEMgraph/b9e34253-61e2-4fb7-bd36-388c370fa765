<!---
{
  "id": "b9e34253-61e2-4fb7-bd36-388c370fa765",
  "depends_on": [],
  "author": "Stephan Bökelmann",
  "first_used": "2025-05-06",
  "keywords": ["systemd", "linux", "services", "unit files", "init system"]
}
--->

# Introduction to `systemd`

> In this exercise you will learn how to interact with the `systemd` init system in a modern Linux environment. Furthermore we will explore how to create, manage, and enable custom unit files for services.

### Introduction

`systemd` is the standard init system used in most modern Linux distributions, including Debian, Ubuntu, Fedora, and Arch Linux. It is responsible for bootstrapping the user space and managing system processes after booting. Unlike older init systems like SysV or Upstart, `systemd` uses declarative unit files and integrates tightly with various aspects of the system.

Understanding `systemd` is crucial for system administrators and developers alike, as it provides tools not just for managing services but also for logging, device management, mounting filesystems, and more. In this exercise, we focus on service management — inspecting, starting, stopping, restarting, and enabling services — as well as creating and configuring custom unit files.

You will become familiar with `systemctl`, the command-line interface to interact with `systemd`, and understand how to define your own services using unit files. By mastering these basics, you lay the foundation for deeper system administration tasks.

### Further Readings and Other Sources

* [systemd man pages](https://www.freedesktop.org/software/systemd/man/systemd.html)
* [DigitalOcean: Understanding Systemd Units and Unit Files](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files)
* [Arch Wiki: systemd](https://wiki.archlinux.org/title/systemd)
* [Red Hat: Introduction to systemd](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/chap-managing_services_with_systemd)

---

### Tasks

#### 1. Inspecting Services

To check the status of a service, use:

```sh
systemctl status cron
```

This will show you whether the service is active, enabled, or failed. Try this for at least three services on your machine (`ssh`, `cron`, `cups`, etc.).

You can also list all services:

```sh
systemctl list-units --type=service
```

#### 2. Stopping and Restarting Services

To **stop** a service:

```sh
sudo systemctl stop cron
```

To **restart** it:

```sh
sudo systemctl restart cron
```

Try stopping a service, checking its status, then restarting it and confirming it's active again.

#### 3. Enabling and Disabling Services

To enable a service at boot:

```sh
sudo systemctl enable cron
```

To disable it:

```sh
sudo systemctl disable cron
```

To check if it's enabled:

```sh
systemctl is-enabled cron
```

#### 4. Creating a Custom Unit File

Create a simple shell script:

```sh
#!/bin/bash
echo "My custom service started" >> /tmp/myservice.log
```

Save it as `myservice.sh` in `/usr/local/bin/` and make it executable:

```sh
sudo chmod +x /usr/local/bin/myservice.sh
```

Then create a unit file `/etc/systemd/system/mycustom.service`:

```ini
[Unit]
Description=My Custom Service

[Service]
ExecStart=/usr/local/bin/myservice.sh

[Install]
WantedBy=multi-user.target
```

Enable and start the service:

```sh
sudo systemctl daemon-reexec
sudo systemctl enable mycustom.service
sudo systemctl start mycustom.service
```

Verify the log file is written and inspect the status.

#### 5. Reloading Configuration

If you change a unit file, reload the configuration:

```sh
sudo systemctl daemon-reload
```

This is necessary after editing any `.service` file.

#### 6. Viewing Logs with `journalctl`

To view logs of a specific service:

```sh
journalctl -u cron.service
```

For live logs:

```sh
journalctl -u cron.service -f
```

Explore logs for services you’ve used in this exercise.

---

### Questions

1. What is the difference between `systemctl stop` and `disable`?
2. What does `WantedBy=multi-user.target` imply?
3. Why must you reload the systemd daemon after editing unit files?
4. How do unit files contribute to a modular and declarative system configuration?

---

### Advice

Start with familiar services like `ssh` or `cron` when exploring `systemctl`. Don’t worry if you break a service temporarily — most essential services can be restarted easily. The real power of `systemd` lies in its uniformity and its ability to handle dependencies declaratively, so pay close attention to the structure of unit files. If this is your first time creating a `.service` file, keep it simple. Later exercises will delve into timer units, socket activation, and templating. Refer back to this sheet when working with \[\[sysd-timers-001]] or \[\[sysd-sockets-001]].
