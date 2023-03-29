---
author:
  name: Michael Brown
  email: mike.a.brown09@gmail.com
description: 'Set up and configure a basic SIMP installation'
keywords: ["SIMP", "DNS", "LDAP", "Content Management"]
license: '[CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0)'
published: 2019-03-07
modified_by:
  name: Linode
title: 'SIMP Install on Linode'
contributor:
  name: Michael Brown
  link: https://www.linkedin.com/in/michael-brown-8a2a3137/
external_resources:
  - '[Link Title 1](http://www.example.com)'
  - '[Link Title 2](http://www.example.net)'
---
SIMP is an Open Source, fully automated, and extensively tested framework that can either enhance your existing infrastructure or allow you to quickly build one from scratch. Built on the mature Puppet product suite, SIMP is designed around scalability, flexibility, and compliance. 

Initially designed as a turn-key solution for isolated environments, SIMP includes everything you need to get started building repeatable infrastructures at any scale.

The core capabilities of SIMP are; 

* PKI

Fully manage the distribution of key materials throughout your environment and be assured that SIMP services are seamlessly protected.

* LDAP

Centralized accouunt management provides effective real-time administration of users

* Host-based Firewall

System-level network protection and logging across all managed systems. All exposed services running on the system have an enforced firewall policy.

* Secure Remote Access
Encrypt and authenticate remote system communications. Privieged user access restriction and enforced access control groups help detect insider threats and prevent unauthorized access. 

* Audit Management

Audit privileged and invalid user activity by actively collection critical security events across the managed infrastructure.

* Unauthorized Service Prevention

Authorize the services that you want to run either system wide or selectively by host. Disable and report on services that have been enabled without authorization.

Further information can be found on the SIMP website; [here](https://www.simp-project.com/). 


{{< note >}}
This guide is written for a non-root user. Commands that require elevated privileges are prefixed with `sudo`. If you're not familiar with the `sudo` command, you can check our [Users and Groups](/docs/tools-reference/linux-users-and-groups/) guide. Make your linode at a minimum the 4GB Shared.
{{< /note >}}

# Before You Begin
1.  Ensure that you have followed the [Getting Started](/docs/getting-started/) and [Securing Your Server](/docs/security/securing-your-server/) guides, and the Linode's [hostname is set](/docs/getting-started/#setting-the-hostname).
2. Make you have your hostname changed to what you want it to be for the duration of your use of SIMP. Changing DNS is not complex but is alot of administrative work. Please also make sure that the hostname does not have any capital letters in the host names. The installation process will not work if there are any capital letters.

      ```
      Edit /etc/hosts to have the correct hostname for the internal IP Address
      
      Edit /etc/hostname to have the correct hostname
      
      Edit /etc/sysconfig/network if it all ready has your hostname and change it
      
      Run hostnamectl set-hostname #newHostName
      ```
  
3. Update the server

    ```
    yum update
    ```

# Installing SIMP from a Repo
There are many ways that we can install SIMP but we are going to install it via a repository. 

## Enable EPEL Repository

1. Install epel-release, pygpgme and yum-utils

    ```
    yum install epel-release
    
    yum install yum install pygpgme yum-utils
    ```
    
 ## Add SIMP repo
1. Add the simp-project.repo file in /etc/yum.repos.d
      ```
    [simp-project_6_X]
    name=simp-project_6_X
    baseurl=https://packagecloud.io/simp-project/6_X/el/$releasever/$basearch
    gpgcheck=1
    enabled=1
    gpgkey=https://raw.githubusercontent.com/NationalSecurityAgency/SIMP/master/GPGKEYS/RPM-GPG-KEY-SIMP
           https://download.simp-project.com/simp/GPGKEYS/RPM-GPG-KEY-SIMP-6
    sslverify=1
    sslcacert=/etc/pki/tls/certs/ca-bundle.crt
    metadata_expire=300

    [simp-project_6_X_dependencies]
    name=simp-project_6_X_dependencies
    baseurl=https://packagecloud.io/simp-project/6_X_Dependencies/el/$releasever/$basearch
    gpgcheck=1
    enabled=1
    gpgkey=https://raw.githubusercontent.com/NationalSecurityAgency/SIMP/master/GPGKEYS/RPM-GPG-KEY-SIMP
           https://download.simp-project.com/simp/GPGKEYS/RPM-GPG-KEY-SIMP-6
           https://yum.puppet.com/RPM-GPG-KEY-puppetlabs
           https://yum.puppet.com/RPM-GPG-KEY-puppet
           https://apt.postgresql.org/pub/repos/yum/RPM-GPG-KEY-PGDG-96
           https://artifacts.elastic.co/GPG-KEY-elasticsearch
           https://grafanarel.s3.amazonaws.com/RPM-GPG-KEY-grafana
           https://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-$releasever
    sslverify=1
    sslcacert=/etc/pki/tls/certs/ca-bundle.crt
    metadata_expire=300
    ```
    
    
2. Update the $releaseserver variable to be 7 for RHEL7/CentOS7 or 6 for RHEL6/CentOS6. The whitespace and the alignment shown before the additional gpgkey values must be preserved.

## Rebuild the Yum Cache
1. Update the yum cache so that it includes the new repository that we just created
    ```
    yum makecache
    ```

## Install the SIMP Server
1. Select tthe simp-adpater package appropriate for the version of puppet you will be using
    - simp-adapter-foss = FOSS Puppet
    - simp-adapter-pe = Enterprise Puppet

2. We will be using the foss adapter package
  ```
  yum install simp-adapter-foss`
  ```
  - You will get an error saying the certificates are not in the proper files. You can ignore this error at this time

3. Install the remaining SIMP Packages
  ```
  yum install simp
  ```
4. Install all simp-extras
```
yum install simp-extras*
```

# Initial SIMP Server Configuration
In here we will use the 'simp config' and 'simp bootstrap' commands. Before proceeding please make sure that time is correct and synced between all of the servers.

## Simp Configuration
1. Run the simp config utility
```
simp config
```
2. You will be asked a few questions and many of them will be based on your configuration
```
[root@simp ~]# simp config

================================================================================
`simp config` will take you through preparing your infrastructure for bootstrap
based on a pre-defined SIMP scenario.  These preparations include optional
and required general system setup and required Puppet configuration. All changes
will be logged to
               /root/.simp/simp_config.log.20190309T013238
================================================================================
[root@simp ~]# simp config

================================================================================
`simp config` will take you through preparing your infrastructure for bootstrap
based on a pre-defined SIMP scenario.  These preparations include optional
and required general system setup and required Puppet configuration. All changes
will be logged to
               /root/.simp/simp_config.log.20190309T013238
================================================================================


=== cli::simp::scenario ===
The SIMP scenario.

'simp'      = Settings for a full SIMP system. Both the SIMP server
              (this host) and all clients will be running with
              all security features enabled.
'simp_lite' = Settings for a SIMP system in which some security features
              are disabled for SIMP clients.  The SIMP server will
              be running with all security features enabled.
'poss'      = Settings for a SIMP system in which all security features
              for the SIMP clients are disabled.  The SIMP server will
              be running with all security features enabled.

    - recommended value: "simp"
cli::simp::scenario: |simp|
cli::simp::scenario = "simp"
>> Applying: Copy SIMP environment into Puppet environment path... Succeeded
>> Applying: Set $simp_scenario in simp environment's site.pp... Succeeded

=== cli::network::interface ===
The network interface to use to connect to the network.
    - recommended value: "eth0"
cli::network::interface: |eth0|
cli::network::interface = "eth0"

=== cli::network::set_up_nic ===
Whether to activate this NIC now.

This will **reset** the interface, so enter 'no' if you are logged
in via the interface.
    - recommended value: "yes"
cli::network::set_up_nic: |yes| no
cli::network::set_up_nic = false

=== cli::network::hostname ===
The FQDN of the system.
    - os value:          "simp.mikesdevhub.com"
    - recommended value: "simp.mikesdevhub.com"
cli::network::hostname: |simp.mikesdevhub.com|
cli::network::hostname = "simp.mikesdevhub.com"

=== cli::network::ipaddress ===
The IP address of the system.
    - os value:          "198.58.99.6"
    - recommended value: "198.58.99.6"
cli::network::ipaddress: |198.58.99.6|
cli::network::ipaddress = "198.58.99.6"

=== cli::network::netmask ===
The netmask of the system.
    - os value:          "255.255.255.0"
    - recommended value: "255.255.255.0"
cli::network::netmask: |255.255.255.0|
cli::network::netmask = "255.255.255.0"

=== cli::network::gateway ===
The default gateway.
    - os value:          "198.58.99.1"
    - recommended value: "198.58.99.1"
cli::network::gateway: |198.58.99.1|
cli::network::gateway = "198.58.99.1"

=== simp_options::dns::servers ===
A list of DNS servers for the managed hosts.

If the first entry of this list is set to '127.0.0.1', then
all clients will configure themselves as caching DNS servers
pointing to the other entries in the list.

If you have a system that's including the 'named' class and
is *not* in this list, then you'll need to set a variable at
the top of that node entry called $named_server to 'true'.
This will get around the convenience logic that was put in
place to handle the caching entries and will not attempt to
convert your system to a caching DNS server. You'll know
that you have this situation if you end up with a duplicate
definition for File['/etc/named.conf'].
    - os value:          ["96.126.122.5", "96.126.124.5", "96.126.127.5"]
    - recommended value: ["96.126.122.5", "96.126.124.5", "96.126.127.5"]
Enter a space-delimited list (hit enter to accept default value)
simp_options::dns::servers: |96.126.122.5 96.126.124.5 96.126.127.5|
simp_options::dns::servers = ["96.126.122.5", "96.126.124.5", "96.126.127.5"]

=== simp_options::dns::search ===
The DNS domain search string.

Remember to put these in the appropriate order for your environment!
    - os value:          ["members.linode.com", "mikesdevhub.com"]
    - recommended value: ["members.linode.com", "mikesdevhub.com"]
Enter a space-delimited list (hit enter to accept default value)
simp_options::dns::search: |members.linode.com mikesdevhub.com|
simp_options::dns::search = ["members.linode.com", "mikesdevhub.com"]

=== simp_options::trusted_nets ===
A list of subnets to permit, in CIDR notation.

If you need this to be more (or less) restrictive for a given class,
you can override it in Hiera.
    - recommended value: ["198.58.99.0/24"]
Enter a space-delimited list (hit enter to accept default value)
simp_options::trusted_nets: |198.58.99.0/24|
simp_options::trusted_nets = ["198.58.99.0/24"]

=== simp_options::ntpd::servers ===
Your network's NTP time servers.

A consistent time source is critical to a functioning public key
infrastructure, and thus your site security. **DO NOT** attempt to
run multiple production systems using individual hardware clocks!

For many networks, the default gateway (198.58.99.1) provides an NTP server.
    - os value:          []
Enter a space-delimited list (hit enter to skip)
simp_options::ntpd::servers: 0.pool.ntp.org 1.pool.ntp.org
simp_options::ntpd::servers = ["0.pool.ntp.org", "1.pool.ntp.org"]

=== cli::set_grub_password ===
Whether to set the GRUB password on this system.
    - recommended value: "yes"
cli::set_grub_password: |yes| yes
cli::set_grub_password = true

=== grub::password ===
The password to access GRUB.

The value entered is used to set the GRUB password and to generate a hash
stored in grub::password.
Auto-generate the GRUB password? |yes| no
Please enter a password:
GRUB password: *********
Please confirm the password:
Confirm GRUB password: *********
grub::password = "grub.pbkdf2.sha512.10000.98B6D9B5598719025381241E7C7C601D9C849EEABF96B757183420CC345EAF1BBC9B1A235F7D63267E8AC5A4A847FD5E354A2B65EB1A6C1EC18AEB36A6411629.B2182393B66A8E512A0DEACC8DAB944429360D97F55F3D53C8BAAD05E0B5A8ED8CD2A852F3684302F64229C0543FF6F2E5D852839AB3FEE3F208955E2CCC8011"
>> Applying: Set GRUB password... Succeeded

=== cli::set_production_to_simp ===
Whether to set default Puppet environment to 'simp'.

Links the 'production' environment to 'simp', after backing up the
existing production environment.
    - recommended value: "yes"
cli::set_production_to_simp: |yes| yes
cli::set_production_to_simp = true
>> Applying: Set default Puppet environment to 'simp'... Succeeded
>> Applying: Set up Puppet autosign... Succeeded
>> Applying: Update Puppet settings... Succeeded
>> Applying: Ensure Puppet server /etc/hosts entry exists... Succeeded
>> Applying: Create SIMP server <host>.yaml from template... Succeeded
>> Applying: Set PuppetDB master server & port in SIMP server <host>.yaml... Succeeded

=== cli::use_internet_simp_yum_repos ===
Whether to configure SIMP nodes to use internet SIMP and
SIMP dependency YUM repositories.

When this option is enabled, Puppet-managed, YUM repository
configurations will be created for both the SIMP server and
SIMP clients. These configurations will point to official
SIMP repositories.
    - recommended value: "yes"
cli::use_internet_simp_yum_repos: |yes| yes
cli::use_internet_simp_yum_repos = true
>> Applying: Add simp::yum::repo::internet_simp_server class to SIMP server <host>.yaml... Succeeded

=== cli::is_ldap_server ===
Whether the SIMP server will also be the LDAP server.

    - recommended value: "yes"
cli::is_ldap_server: |yes| yes
cli::is_ldap_server = true

=== simp_openldap::server::conf::rootpw ===
The salted LDAP Root password hash.

When set via 'simp config', this password hash is generated from
the password entered on the command line.
Auto-generate the LDAP Root password? |no| no
Please enter a password:
LDAP Root password: *********
Please confirm the password:
Confirm LDAP Root password: *********
simp_openldap::server::conf::rootpw = "{SSHA}Sfhg02zhbct8lvTqIDXBYlBZGGlhT+Gc"
>> Applying: Add simp::server::ldap class to SIMP server <host>.yaml... Succeeded
>> Applying: Set LDAP Root password hash in SIMP server <host>.yaml... Succeeded

=== simp_options::syslog::log_servers ===
The log server(s) to receive forwarded logs.

No log forwarding is enabled when this list is empty.  Only use hostnames
here if at all possible.
Enter a space-delimited list (hit enter to skip)
simp_options::syslog::log_servers:
simp_options::syslog::log_servers = []
>> Applying: Generate interim certificates for SIMP server... Succeeded

=== useradd::securetty ===
A list of TTYs for which the root user can login.

When useradd::securetty is an empty list, the system will satisfy FISMA
regulations, which require root login via any TTY (including the console)
to be disabled.  For some systems, the inability to login as root via the
console is problematic.  In that case, you may wish to include at least
tty0 to the list of allowed TTYs, despite the security risk.

    - recommended value: []
Enter a space-delimited list (hit enter to accept default value)
useradd::securetty: || ttyS0 tty1
useradd::securetty = ["ttyS0", "tty1"]
>> Applying: Disallow inapplicable 'simp' user in SIMP server <host>.yaml... Succeeded
>> Applying: Check for login lockout risk...
WARNING: Locking bootstrap due to potential login lockout.
See /root/.simp/simp_bootstrap_start_lock for details
Failed

=== svckill::mode ===
Strategy svckill should use when it encounters undeclared services.

'enforcing' = Shut down and disable all services not listed in your
              manifests or the exclusion file
'warning'   = Only report what undeclared services should be shut
              down and disabled, without actually making the changes
              to the system

NOTICE: svckill is the mechanism that SIMP uses to comply with the
requirement that no unauthorized services are running on your system.
If you are fully aware of all services that need to be running on the
system, including any custom applications, use 'enforcing'.  If you
first need to ascertain which services should be running on the system,
use 'warning'.
    - recommended value: "warning"
svckill::mode: |warning|
IMPORTANT: Once you have examined the list of undeclared services
reported by svckill and have determined which of those should be
allowed, register the allowed services with svckill and then change
svckill::mode to 'enforcing'.  See svckill::ignore and svckill::ignore_files.
svckill::mode = "warning"
>> Applying: Write SIMP global hieradata to YAML file.... Succeeded
>> Applying: Write answers to YAML file.... Succeeded

================================================================================

Summary of Applied Changes
  Copy of SIMP environment into Puppet environment path succeeded
  Setting of $simp_scenario in the simp environment's site.pp succeeded
  Setting of GRUB password succeeded
  Setting 'simp' to the Puppet default environment succeeded
  Setup of autosign in /etc/puppetlabs/puppet/autosign.conf succeeded
  Update to Puppet settings in /etc/puppetlabs/puppet/puppet.conf succeeded
  Update to /etc/hosts to ensure puppet server entries exist succeeded
  Creation of simp.mikesdevhub.com.yaml succeeded
  Setting of PuppetDB master server & port in simp.mikesdevhub.com.yaml succeeded
  Addition of simp::yum::repo::internet_simp_server to simp.mikesdevhub.com.yaml class list succeeded
  Addition of simp::server::ldap to simp.mikesdevhub.com.yaml class list succeeded
  Setting of LDAP Root password hash in simp.mikesdevhub.com.yaml succeeded
  Interim certificate generation for 'simp.mikesdevhub.com' succeeded
  Disallow of inapplicable, local 'simp' user in simp.mikesdevhub.com.yaml succeeded
  'simp bootstrap' has been locked due to potential login lockout.
  * See /root/.simp/simp_bootstrap_start_lock for details
  .../environments/simp/data/simp_config_settings.yaml created
  /root/.simp/simp_conf.yaml created

Detailed log written to /root/.simp/simp_config.log.20190309T013238
```

3. Now you will have to create a local system user. This is needed in case you ever get locked out. Run the following commands to get to the manifest directory

```
cd /etc/puppetlabs/code/environments/production/manifests
touch sys_users.pp
chown root.puppet sys_users.pp
chmod 640 sys_users.pp
vim sys_users.pp
````

4. The file contents should be something like this

```
class sys_users (
$pam = simplib::lookup('simp_options::pam', { 'default_value' => false }),
){

group { 'local_admin':
  gid => '454'
}

user { 'local_admin':
  ensure  => 'present',
  allowdupe => 'false',
  uid => '454',
  gid => '454',
  groups => 'wheel',
  home => '/home/local_admin',
  password => '$1$yWFBkR/X$9zhvf60P8sTz9QPQtqwYk1',
  require => Group['local_admin'],
}

if $pam {
  include '::pam'

  pam::access::rule { 'allow_local_admin':
    users => ['local_admin'],
    origins => ['ALL'],
    comment => 'The local user, used to locally login to the system in the case of a lockout.'
    }
  pam::access::rule { 'allow_postgres':
    users => ['postgres'],
    origins => ['ALL'],
    comment => 'The local user, used to locally login to the system in the case of a lockout.'
    }

  pam::access::rule { 'allow_sonar':
        users => ['sonar'],
        origins => ['ALL'],
        comment => 'The local user, used to locally login to the system in the case of a lockout.'
   }
}

sudo::user_specification { 'default_local_admin':
  user_list => ['local_admin'],
  runas => 'root',
  cmnd => ['/bin/su root', '/bin/su - root', '/bin/sudosh'],
  passwd => false
}

}
```

Now the password is a hashed password to get the hashed password run the following command

```
openssl passwd -1
```

This will allow you to pick the password of your choosing. You can ignore the pam access rule for postgres and sonar. Those can be removed from your manifest file.

4. You can create the necessary host file or you can add it to the default.yaml file. I suggest you putting in the default so all servers have the same local admin account. Run the following commands

```
cd /etc/puppetlabs/code/environments/production/data
vim default.yaml
```

In that file add the following

```
classes:
- 'sys_users'
```

Now we can remove the bootstrap lock file

```
rm /root/.simp/simp_bootstrap_start_lock
```
##SIMP Bootstrap
Since we now have the configuration completed for the SIMP server we can run bootstrap

1. Run the following command

```
simp bootstrap
```

The output from the command should look like something like this:

```
[root@simp data]# simp bootstrap
=== Starting SIMP Bootstrap ===
> The log can be found at '/root/.simp/simp_bootstrap.log.20190309T020606'
> Successfully disabled non-bootstrap puppet agent
> Interrupts will be captured and ignored to ensure bootstrap integrity.
> Killing connection to puppetdb
> Killing all remaining puppet processes
> Successfully removed /var/run/puppetlabs/puppetserver/*
> Existing puppetserver certificates have been found in
>     /etc/puppetlabs/puppet/ssl
> If this server has no registered agents, those certificates can be safely removed.
> Otherwise, although removing them will ensure consistency, manual
> steps may be required to ensure connectivity with existing Puppet clients.
> (See https://docs.puppet.com/puppet/latest/ssl_regenerate_certificates.html)
> Regardless, if removed, new puppetserver certificates will be generated
> automatically.
> Do you wish to remove existing puppetserver certificates? (yes|no) yes
> Successfully removed /etc/puppetlabs/puppet/ssl/*
> Validating site puppet code
> Configuring the puppetserver to listen on port 8150
> Successfully backed up /etc/puppetlabs/puppetserver/conf.d/webserver.conf to /root/.simp/simp_bootstrap.backup.20190309T020606/etc/puppetlabs/puppetserver/conf.d
> Successfully backed up /etc/sysconfig/puppetserver to /root/.simp/simp_bootstrap.backup.20190309T020606/etc/sysconfig
> Successfully backed up /etc/puppetlabs/puppet/auth.conf to /root/.simp/simp_bootstrap.backup.20190309T020606/etc/puppetlabs/puppet
> Removed /etc/puppetlabs/puppet/auth.conf
> Successfully configured /etc/sysconfig/puppetserver to use a temporary cache
> Successfully configured /etc/puppetlabs/puppetserver/conf.d/webserver.conf with bootstrap settings
Service not running so cannot be reloaded
> Running puppet agent, with --tags pupmod,simp
> Waiting for puppetserver to accept connections on port 8150
> Track => ######################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################
> Relabeling filesystem for selinux (this may take a while...)
> Running puppet without tags
> Waiting for puppetserver to accept connections on port 8140
> Track => ###############################################
> Waiting for puppetserver to accept connections on port 8140
> Track => #############
=== SIMP Bootstrap Finished! ===
> Duration of complete bootstrap: 657.188488761 seconds
> /root/.simp/simp_bootstrap.log.20190309T020606 contains details of the bootstrap actions performed.
> Prior to operation, you must reboot your system.
> Run `puppet agent -t` after reboot to complete the bootstrap process.
> It may take a few minutes before the puppetserver accepts agent
> connections after boot.
```
{{< note >}}
If progress bars are of equal length and the bootstrap finishes quickly, a problem has occurred. This is most likely due to an error in SIMP configuration. Refer to the previous step and make sure that all configuration options are correct.

If this happens, you can debug by either looking at the log files or by running 

```
puppet agent -t --masterport=8150
```
{{< /note >}}

2. Type the following command allow the server to reboot. Remember when it comes back we will have to run puppet

```
reboot
```

3. When the server comes back you will have to log in as the local_admin account and then run the following command to become root

```
sudo su - root
```

4. When we run puppet we get the following output

```
[root@simp ~]# puppet agent -t
Info: Using configured environment 'production'
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Retrieving locales
Info: Loading facts
Info: Caching catalog for simp.mikesdevhub.com
Info: Applying configuration version '1552101712'
Warning: svckill: Would have killed:
  svckill: rhel-domainname.service
Notice: Applied catalog in 17.40 seconds
```

5. The service rhel-domainname we will have to update in our puppetry. Run the following commands
```
cd /etc/puppetlabs/code/environments/production/data
vim default.yaml
```

Add the following code to the bottom of the file

```
svckill::ignore:
- 'rhel-domainname'
```

Save the file and exit it. Then run puppet

```
puppet agent -t
```

Your output then should be something like this

```
[root@simp data]# puppet agent -t
Info: Using configured environment 'production'
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Retrieving locales
Info: Loading facts
Info: Caching catalog for simp.mikesdevhub.com
Info: Applying configuration version '1552105032'
Notice: Applied catalog in 19.87 seconds
```
