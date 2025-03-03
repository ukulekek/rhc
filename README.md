# rhc
![rhc](https://github.com/linux-system-roles/template/workflows/tox/badge.svg)

An ansible role which registers/connects RHEL systems to Red Hat.

## Requirements

subscription-manager and insights-client. Both are available from the standard
RHEL repositories, and subscription-manager is installed by default.

The role requires modules from `community.general`.  If you are using
`ansible-core`, you must install the `community.general` collection.  Use the
file `meta/collection-requirements.yml` to install it:
```
ansible-galaxy collection install -vv -r meta/collection-requirements.yml
```
If you are using Ansible Engine 2.9, or are using an Ansible bundle which
includes these collections/modules, you should have to do nothing.

## Role Variables

    rhc_state: present

Whether the system is connected to Red Hat; valid values are `present`
(to ensure registration/connection), and `absent`.

    rhc_organization: "your-organization"

The organization of the user. This *must* be specified when registering if
either:
- the user belongs to more than one organization
- using activation keys (see `rhc_auth` below)

    rhc_auth: {}

The authentication method used to connect a system. This must be specified
in case a system may need to connect (e.g. in case it was not before).
There are few possible authentication methods; only one can be specified at
a time.

**NB**: the variables used for authentication are considered secrets, and thus
they ought to be secured. We recommend the usage of Ansible Vault as source
for them. The references below only describe which keys exists and what they
are for.

For username/password, authentication, specify the `login` dictionary:
```yaml
rhc_auth:
  login:
    username: "your-username"
    password: "your-password"
```

For activation keys authentication, specify the `activation_keys` dictionary,
together with `rhc_organization`:
```yaml
rhc_auth:
  activation_keys:
    keys: ["key-1", ...]
rhc_organization: "your-organization"
```

    rhc_server: {}

The details of the registration server to connect to:
```yaml
rhc_server:
  hostname: "registration-hostname"
  port: 443
  prefix: "registration-server-prefix"
  insecure: false
```
- `hostname` is the hostname of the registration server
- `port` is the port to which connect to on the server
- `prefix` is the prefix (starting with `/`) for the API calls to the
  registration server
- `insecure` specifies whether to disable the validation of the SSL certificate
  of the registration server

## Dependencies

None.

## Example Playbook

Simple Registration to Red Hat including Insights, assuming authentication
using username & password:

```yaml
- hosts: all
  vars:
    rhc_auth:
      login:
        username: "your-username"
        password: !vault |
          $ANSIBLE_VAULT;1.2;AES256;dev
          ....
  roles:
    - linux-system-roles.rhc
```

## License

MIT
