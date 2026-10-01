<!--
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2020, 2021 Aaron Raimist
SPDX-FileCopyrightText: 2020-2024 MDAD project contributors
SPDX-FileCopyrightText: 2020-2024 Slavi Pantaleev
SPDX-FileCopyrightText: 2021 Kim Brose
SPDX-FileCopyrightText: 2021 Luca Di Carlo
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Julian Foad
SPDX-FileCopyrightText: 2022 Olivér Falvai
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Felix Stupp
SPDX-FileCopyrightText: 2023 Michael Hollister
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024-2026 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up Grafana

This is an [Ansible](https://www.ansible.com/) role which installs [Grafana](https://grafana.com/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

Grafana is a web-based tool for visualizing your [Prometheus](https://prometheus.io/) metrics (time-series).

See the project's [documentation](https://grafana.com/docs/) to learn what Grafana does and why it might be useful to you.

> [!WARNING]
> Metrics and graphs contain a lot of information, and anyone who has access to them can make an educated guess about your server usage patterns. This especially applies to small personal/family scale homeservers, where the number of samples is fairly limited. Analyzing the metrics over time, one might be able to figure out your life cycle, such as when you wake up, go to bed, etc. Before enabling (anonymous) access, you should carefully evaluate the risk, and if you do enable it, it is highly recommended to change your Grafana password from the default one.

## Adjusting the playbook configuration

To enable Grafana with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# grafana                                                              #
#                                                                      #
########################################################################

grafana_enabled: true

########################################################################
#                                                                      #
# /grafana                                                             #
#                                                                      #
########################################################################
```

### Set the hostname

To enable Grafana you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
grafana_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

### Setting username and password for the admin user (optional)

By default Grafana creates a user with `admin` as the username and password. You are asked to change the credentials on first login. If this is insecure for you, you can change them beforehand by adding the following configuration to your `vars.yml` file:

```yaml
grafana_default_admin_user: YOUR_ADMIN_USER_USERNAME_HERE

# The value can be generated with `pwgen -s 64 1` or in another way.
grafana_default_admin_password: YOUR_ADMIN_USER_PASSWORD_HERE
```

>[!NOTE]
> Changing those username/password subsequently won't update them.

### Allowing anonymous access (optional)

By default viewing graphs requires you to log in to the instance. If you want to publicly share graphs (e.g. when asking for help in [`#synapse:matrix.org`](https://matrix.to/#/#synapse:matrix.org) about the Matrix homeserver configuration), you'll want to allow anonymous access by adding the following configuration to your `vars.yml` file:

```yaml
grafana_anonymous_access: true
```

### Extending the configuration

There are some additional things you may wish to configure about the service.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `grafana_environment_variables_additional_variables` variable

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, Grafana becomes available at the specified hostname like `https://example.com`.

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu grafana` (or how you/your playbook named the service, e.g. `mash-grafana`).
