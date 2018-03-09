# aspects_firewalld
Install or remove firewalld and configure firewalld zones.

This role templates the `/etc/firewalld/zones/<zone>.xml` files in order to apply the configuration.

# Requirements

Set ```hash_behaviour=merge``` in your ansible.cfg file.

# Role Variables
### aspects_firewalld_enabled

Default is ```False```.

Set to ```True``` to run tasks in this role.

Set to ```False``` to block all tasks in this role from running.

### aspects_firewalld_packages
A dictionary/hash of packages to install.

Use this pattern:

```yaml
aspects_firewalld_packages:
  <package key>:
    state: "<present or latest>"
    <ansible_distribution>:
      <ansible_distribution_version or ansible_distribution_major_version>: "<package name>"
```
Set ```state``` to "default" if you wish to list a package but not install it.

Check the [tasks/aptInstallpackages.yml](aptInstallpackages.yml) or [tasks/yumInstallPackages.yml](yumInstallPackages.yml) files to find out what values are accepted for the ```ansible_distribution_*``` variables.

### aspects_firewalld_zones
A dictionary/hash of the firewalld rules to configure in the zone files.

Use the following pattern:
```yaml
aspects_firewalld_zone:
  <your identifier>:
    state: "<mandatory: present or absent>"
    key: "<mandatory: The name of the zone you would use when running firewalld on the command line, will be used to name the xml file.>"
    name: "<optional: A human readable version of the key>"
    description: "<optional: A description of the zone.>"
    <tag 1>:
      state: "<mandatory: enabled or disabled>"
      <tag attribute 1>: "<value for attribute 1>"
      ...
      <tag attribute N>: "<value for attribute N>"
    ...
    <tag N>:
      state: "<mandatory: enabled or disabled>"
      <tag attribute 1>: "<value for attribute 1>"
      ...
      <tag attribute N>: "<value for attribute N>"
```

#### aspects_firewalld_zones.rules
Text blocks to configure rich rules.

```yaml
aspects_firewalld_zones:
  rules:
    state: "<enabled or disabled>"
    value: |
    <rule> ... </rule>

```

Use the [http://www.firewalld.org/documentation/man-pages/firewalld.zone.html](firewalld.org documentation) on zone files to find the available tags, as well as how to use rich rules.


# Example Playbook

```yaml
- hosts:
  - all
  vars:
    aspects_firewalld_enabled: True
    aspects_firewalld_zones:
      public:
        state: "absent"
        key: "public"
      public_eth0:
        state: "present"
        name: "Public eth0"
        key: "public_eth0"
        description: "For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted."
        interfaces:
          eth0:
            state: "enabled"
            name: "eth0"
        services:
          ssh:
            state: "enabled"
            name: "ssh"
          dhcpv6_client:
            state: "enabled"
            name: "dhcpv6-client"
        ports:
          port80:
            state: "enabled"
            name: "80"
            protocol: "tcp"
          port8021duplicati:
            state: "enabled"
            name: "8021"
            protocol: "tcp"
        protocols:
          vmtp:
            state: "enabled"
            value: "vmtp"
      public_eth1:
        state: "present"
        name: "Public"
        key: "public_eth1"
        description: "Blah"
        interfaces:
          eth1dddd:
            state: "enabled"
            name: "eth1"
        services:
          ssh:
            state: "disabled"
            name: "ssh"
          dhcpv6_client:
            state: "enabled"
            name: "dhcpv6-client"
        ports:
          port80:
            state: "disabled"
            name: "80"
            protocol: "tcp"
          port8021duplicati:
            state: "enabled"
            name: "8021"
            protocol: "tcp"
        protocols:
          vmtp:
            state: "enabled"
            value: "vmtp"
  roles:
  - aspects_firewalld
```

# License

MIT