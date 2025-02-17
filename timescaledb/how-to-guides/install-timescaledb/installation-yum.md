## yum Installation [](installation-yum)

This installs both TimescaleDB *and* PostgreSQL via `yum`
(or `dnf` on Fedora).

**Note: TimescaleDB requires PostgreSQL 12, 13, or 14.**

#### Prerequisites

- RHEL/CentOS 7 (or Fedora equivalent) or later

#### Build and install

<highlight type="warning">
 If you have another PostgreSQL installation not
via `yum`, this could cause problems.
If you wish to maintain your current version of PostgreSQL outside of `yum`,
we recommend installing from source.  Otherwise please be
sure to remove non-`yum` installations before using this method.
</highlight>

You'll need to [download the correct PGDG from PostgreSQL][pgdg] for
your operating system and architecture and install it:
```bash
# Download PGDG:
sudo yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-$(rpm -E %{rhel})-x86_64/pgdg-redhat-repo-latest.noarch.rpm

```

Add TimescaleDB's third party repository and install TimescaleDB,
which downloads any dependencies it needs from the PostgreSQL repo:
```bash
# Add timescaledb repo
sudo tee /etc/yum.repos.d/timescale_timescaledb.repo <<EOL
[timescale_timescaledb]
name=timescale_timescaledb
baseurl=https://packagecloud.io/timescale/timescaledb/el/$(rpm -E %{rhel})/\$basearch
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://packagecloud.io/timescale/timescaledb/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
EOL

sudo yum update -y

# disable builtin postgres packages on CentOs 8
if command -v dnf; then sudo dnf -qy module disable postgresql; fi

# Now install appropriate package for PG version
sudo yum install -y timescaledb-2-postgresql-13
```

#### Upgrading from TimescaleDB 1.x
If you are upgrading from TimescaleDB 1.x, the EXTENSION must be updated
in the database as discussed in [Updating Timescale to 2.0][update-tsdb-2].

#### Configure your database

There are a [variety of settings that can be configured][config] for your
new database. At a minimum, you need to update your `postgresql.conf`
file to include `shared_preload_libraries = 'timescaledb'`.
The easiest way to get started is to run `timescaledb-tune`, which is
installed by default when using `yum`:
```bash
sudo timescaledb-tune
```

This ensures that our extension is properly added to the parameter
`shared_preload_libraries` as well as offer suggestions for tuning memory,
parallelism, and other settings.

To get started you'll need to restart PostgreSQL and add
a `postgres` [superuser][createuser] (used in the rest of the docs). Please
refer to your distribution for how to restart services, for example:
```
sudo -u postgres service postgres-13 start
```

[pgdg]: https://yum.postgresql.org/repopackages.php
[config]: /how-to-guides/configuration/
[createuser]: https://www.postgresql.org/docs/current/sql-createrole.html
[update-tsdb-2]: /how-to-guides/update-timescaledb/update-timescaledb-2/
