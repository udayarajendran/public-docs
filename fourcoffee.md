`fourcoffee.md` file for SSSD, SAMBA, KRB5 and WINBIND setup:

Note: 
EXAMPLE DOMAIN USED- FOURCOFFEE.COM
EXAMPLE DOMAIN CONTROLLER USED- fc-dc-01

# Domain Join and Samba Configuration

This Markdown file provides instructions for setting up a Linux machine to join an Active Directory (AD) domain, configure Kerberos authentication, set up Samba for file sharing, and use SSSD for managing domain users.

## Prerequisites

Ensure the following packages are installed on system:

sssd: Manages domain authentication and users.
cifs-utils: Mounts and manages SMB/CIFS shares.
chrony: Synchronizes system time with servers.
samba: Provides file and printer sharing.
samba-client: Access and manage Samba shares.
samba-common: Common Samba files and configuration.
samba-winbind: Connects AD users and groups.
samba-winbind-clients: Tools for AD authentication support.
krb5-workstation: Kerberos client tools for authentication.

```bash
sudo dnf install realmd sssd cifs-utils chrony samba samba-client samba-common samba-winbind samba-winbind-clients krb5-workstation
```

## Configuration Files

### 1. Kerberos Configuration (`/etc/krb5.conf`)

The `krb5.conf` file contains the Kerberos authentication settings. Ensure the following content is configured correctly for your environment:


#### /etc/krb5.conf
```ini
includedir /etc/krb5.conf.d/

[logging]
    default = FILE:/var/log/krb5libs.log
    kdc = FILE:/var/log/krb5kdc.log
    admin_server = FILE:/var/log/kadmind.log

[libdefaults]
    default_realm = FOURCOFFEE.COM
    dns_lookup_realm = false
    dns_lookup_kdc = true

[realms]
    FOURCOFFEE.COM = {
        kdc = fc-dc-01.fourcoffee.com
        admin_server = fc-dc-01.fourcoffee.com
    }

[domain_realm]
    .fourcoffee.com = FOURCOFFEE.COM
    fourcoffee.com = FOURCOFFEE.COM
```

### 2. Samba Configuration (`/etc/samba/smb.conf`)

The `smb.conf` file configures Samba for sharing files and printers with AD users.

#### /etc/samba/smb.conf
```ini
[global]
    workgroup = FOURCOFFEE
    realm = FOURCOFFEE.COM
    security = ads
    encrypt passwords = yes

    # Use winbind for domain users and groups
    idmap config * : range = 10000-20000
    idmap config FOURCOFFEE : backend = rid
    idmap config FOURCOFFEE : range = 20001-30000

    # Enable winbind to resolve users and groups
    winbind use default domain = yes
    winbind offline logon = yes
    template shell = /bin/bash
    template homedir = /home/%U

    # Enable ACL Support
    vfs objects = acl_xattr
    map acl inherit = Yes
    store dos attributes = Yes

    # Enable Secure Kerberos Communication
    client signing = mandatory
    client schannel = yes
    server signing = mandatory
    server schannel = yes

    # Optional Printing Support
    # printing = cups
    # printcap name = cups
    # load printers = yes
    # cups options = raw
    # Samba Usershares
    include = /etc/samba/usershares.conf

[homes]
    comment = Home Directories
    valid users = %S, %D%w%S
    browseable = No
    read only = No
    inherit acls = Yes

[printers]
    comment = All Printers
    path = /var/tmp
    printable = Yes
    create mask = 0600
    browseable = No

[print$]
    comment = Printer Drivers
    path = /var/lib/samba/drivers
    write list = @printadmin root
    force group = @printadmin
    create mask = 0664
    directory mask = 0775
```

### 3. SSSD Configuration (`/etc/sssd/sssd.conf`)

The `sssd.conf` file is used for integrating with AD to manage user accounts and authentication.


#### /etc/sssd/sssd.conf
```ini
[sssd]
domains = fourcoffee.com
config_file_version = 2
services = nss, pam

[domain/fourcoffee.com]
default_shell = /bin/bash
krb5_store_password_if_offline = True
cache_credentials = True
krb5_realm = FOURCOFFEE.COM
realmd_tags = manages-system joined-with-adcli
id_provider = ad
fallback_homedir = /home/%u
ad_domain = fourcoffee.com
use_fully_qualified_names = False
ldap_id_mapping = True
access_provider = ad
ad_gpo_ignore_unreadable = True
ad_gpo_map_network = +nx
```

## 4. Chrony Configuration (Time Synchronization)

Ensure that your system clock NTP/PTP is synchronized with the domain controllers using Chrony or any other time synchronization method suitable for environment.

## Steps

1. **Join the Domain**  
   Use the `realm` command to join the domain:
   ```bash
   sudo realm join --user=Administrator FOURCOFFEE.COM
   ```

2. **Start Services**  
   Enable and start the required services:
   ```bash
   sudo systemctl enable sssd #System Security Services Daemon
   sudo systemctl start sssd
   sudo systemctl enable winbind #Samba Winbind 
   sudo systemctl start winbind
   sudo systemctl enable nmb #NetBIOS
   sudo systemctl start nmb
   sudo systemctl enable smb #Samba
   sudo systemctl start smb
   sudo systemctl enable chronyd
   sudo systemctl start chronyd
   ```

3. **Testing**  
   - Verify Kerberos authentication:
     ```bash
     kinit <domain-user>
     klist
     ```
   - Test domain users with `id` and `getent` commands:
     ```bash
     id <domain-user>
     getent passwd <domain-user>
     ```

## Notes

- Modify the domain-specific details (`FOURCOFFEE.COM`, `fc-dc-01.fourcoffee.com`.) to match the environment.
- Ensure the firewall allows necessary ports for Samba and AD communication.


## Author
`fourcoffee.md` was created by [Udayakumar Rajendran] on [2024-10-09].
