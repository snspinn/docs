## Homebrew [](homebrew)

This installs both TimescaleDB *and* PostgreSQL via Homebrew.

**Note: TimescaleDB requires PostgreSQL 12, 13, or 14.**

#### Prerequisites

- [Homebrew][]

#### Build and install

<highlight type="warning">
 If you have another PostgreSQL installation
(such as through Postgres.app), the following instructions could
cause problems. If you wish to maintain your current version of PostgreSQL
outside of Homebrew we recommend installing from source.  Otherwise please be
sure to remove non-Homebrew installations before using this method.
</highlight>

```bash
# Add our tap
brew tap timescale/tap

# To install
brew install timescaledb

# Post-install to move files to appropriate place
/usr/local/bin/timescaledb_move.sh
```

#### Configure your database

There are a [variety of settings that can be configured][config] for your
new database. At a minimum, you need to update your `postgresql.conf`
file to include `shared_preload_libraries = 'timescaledb'`.
The easiest way to get started is to run `timescaledb-tune`, which is
installed as a dependency when you install via Homebrew:
```bash
timescaledb-tune
```

This ensures that our extension is properly added to the parameter
`shared_preload_libraries` as well as offer suggestions for tuning memory,
parallelism, and other settings.

To get started you'll now need to restart PostgreSQL and add
a `postgres` superuser (used in the rest of the docs):

```bash
# Restart PostgreSQL instance
brew services restart postgresql

# Add a superuser postgres:
createuser postgres -s
```

<highlight type="tip">
Our standard binary releases are licensed under the Timescale License,
which allows to use all our capabilities.
If you want to use a version that contains _only_ Apache 2.0 licensed
code, you should use `brew install timescaledb --with-oss-only`.
</highlight>

[config]: /how-to-guides/configuration/postgres-config/
[Homebrew]: https://brew.sh/
[contact]: https://www.timescale.com/contact
[slack]: https://slack.timescale.com/
