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

## Before You Begin
1.  Ensure that you have followed the [Getting Started](/docs/getting-started/) and [Securing Your Server](/docs/security/securing-your-server/) guides, and the Linode's [hostname is set](/docs/getting-started/#setting-the-hostname).
2. Make you have your hostname changed to what you want it to be for the duration of your use of SIMP. Changing DNS is not complex but is alot of administrative work.

      Edit /etc/hosts to have the correct hostname for the internal IP Address
      
      Edit /etc/hostname to have the correct hostname
      
      Edit /etc/sysconfig/network if it all ready has your hostname and change it
      
      Run hostnamectl set-hostname #newHostName
  
3. Update the server

    yum update
