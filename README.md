# Ansible Role: PostgreSQL
[![GitHub license](https://img.shields.io/github/license/5u623l20/ansible-role-pgsql)](https://github.com/5u623l20/ansible-role-pgsql/blob/master/LICENSE)
[![Travis](https://img.shields.io/travis/5u623l20/ansible-role-pgsql)](https://travis-ci.org/5u623l20/ansible-role-pgsql)
[![Ansible role](https://img.shields.io/ansible/role/47595)](https://galaxy.ansible.com/5u623l20/pgsql)
[![Ansible Quality](https://img.shields.io/ansible/quality/47595)](https://galaxy.ansible.com/5u623l20/pgsql)

Installs and configures PostgreSQL server on *nix alike OS.

## Requirements

No special requirements; note that this role requires root access, so either run it in a playbook with a global `become: yes`, or invoke the role in your playbook like:

```yaml
- hosts: database
  roles:
    - role: 5u623l20.pgsql
      become: yes
```

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
pgsql_restarted_state: "restarted"
```

Set the state of the service when configuration changes are made. Recommended values are `restarted` or `reloaded`.


```yaml
pgsql_user: postgres
pgsql_group: postgres
```

The user and group under which PostgreSQL will run.

```yaml
pgsql_unix_socket_directories:
  - /var/run/postgresql
```

The directories (usually one, but can be multiple) where PostgreSQL's socket will be created.

```yaml
pgsql_service_state: started
pgsql_service_enabled: true
```

Control the state of the postgresql service and whether it should start at boot time.

```yaml
pgsql_global_config_options:
  - option: unix_socket_directories
    value: '{{ pgsql_unix_socket_directories | join(",") }}'
```

Global configuration options that will be set in `postgresql.conf`. Note that for RHEL/CentOS 6 (or very old versions of PostgreSQL), you need to at least override this variable and set the `option` to `unix_socket_directory`.

```yaml
pgsql_hba_entries:
  - { type: local, database: all, user: postgres, auth_method: peer }
  - { type: local, database: all, user: all, auth_method: peer }
  - { type: host, database: all, user: all, address: '127.0.0.1/32', auth_method: md5 }
  - { type: host, database: all, user: all, address: '::1/128', auth_method: md5 }
```

Configure [host based authentication](https://www.postgresql.org/docs/current/static/auth-pg-hba-conf.html) entries to be set in the `pg_hba.conf`. Options for entries include:

- `type` (required)
- `database` (required)
- `user` (required)
- `address` (one of this or the following two are required)
- `ip_address`
- `ip_mask`
- `auth_method` (required)
- `auth_options` (optional)

If overriding, make sure you copy all of the existing entries from `defaults/main.yml` if you need to preserve existing entries.

```yaml
pgsql_locales:
- 'en_US.UTF-8'
```

(Debian/Ubuntu only) Used to generate the locales used by PostgreSQL databases.

```yaml
pgsql_databases:
  - name: exampledb # required; the rest are optional
    lc_collate: # defaults to 'en_US.UTF-8'
    lc_ctype: # defaults to 'en_US.UTF-8'
    encoding: # defaults to 'UTF-8'
    template: # defaults to 'template0'
    login_host: # defaults to 'localhost'
    login_password: # defaults to not set
    login_user: # defaults to 'pgsql_user'
    login_unix_socket: # defaults to 1st of pgsql_unix_socket_directories
    port: # defaults to not set
    owner: # defaults to pgsql_user
    state: # defaults to 'present'
    ```

A list of databases to ensure exist on the server. Only the `name` is required; all other properties are optional.

```yaml
pgsql_users:
  - name: jdoe #required; the rest are optional
    password: # defaults to not set
    encrypted: # defaults to not set
    priv: # defaults to not set
    role_attr_flags: # defaults to not set
    db: # defaults to not set
    login_host: # defaults to 'localhost'
    login_password: # defaults to not set
    login_user: # defaults to '{{ pgsql_user }}'
    login_unix_socket: # defaults to 1st of pgsql_unix_socket_directories
    port: # defaults to not set
    state: # defaults to 'present'
```

A list of users to ensure exist on the server. Only the `name` is required; all other properties are optional. If password is not set a random password is generated and the password is saved in the relative folder `credentials/{{ansible_hostname}}/pgsqlpassword`. For an empty password please set it to `[]`

```yaml
postgres_users_no_log: true
```

Whether to output user data (which may contain sensitive information, like passwords) when managing users.

pgsql_version: # defaults to 11, change with supported values from Postgresql repos
pgsql_data_dir: [OS-specific]
pgsql_bin_path: [OS-specific]
pgsql_config_path: [OS-specific]
pgsql_daemon: [OS-specific]
pgsql_packages: [OS-specific]
pgsql_python_library: [OS-specific] # defaults to python3-psycopg2

OS-specific variables that are set by include files in this role's `vars` directory. These shouldn't be overridden.

## Dependencies

None.

## Example Playbook

```yaml
- hosts: database
  become: yes
  roles:
    - 5u623l2.pgsql
  vars:
    pgsql_databases:
      - name: 5u623l20
    pgsql_users:
      - name: 5u623l20
```

## License

BSD-3-CLAUSE

## Author Information

Created in 2020 by [Muhammad Moinur Rahman](https://bofh.am)
Modified from [Jeff Geerling's](https://www.jeffgeerling.com/) [Ansible Role: PostgreSQL](https://github.com/geerlingguy/ansible-role-postgresql/)

