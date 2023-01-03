---
author: Gary Ossewaarde
title: Linux - AD Auth with SSSD
categories: ['linux']
tags: ['linux', 'active directory']
date: 2022-12-30T21:45:28-04:00
---

Here are some brief notes on authenticating Linux users with AD - including using SSH keys. All of this was done on Ubuntu 22.04. 

<!--more-->

1. Install necessary packages: `sudo apt install sssd-ad sssd-tools realmd adcli`

2. Join the domain: `sudo realm join <domain name>`

3. Enable home directories with pam: `sudo pam-auth-update --enable mkhomedir`

4. Configure SSSD. Note: the line "ldap_user_ssh_public_key = sshPublicKey" becomes important in a later step, but included for completeness of sssd.conf

    ```
    # /etc/sssd/sssd.conf
    [sssd]
    domains = <domain name>
    config_file_version = 2
    services = nss, pam, sudo

    [domain/<domain name>]
    default_shell = /bin/bash
    krb5_store_password_if_offline = True
    cache_credentials = True
    krb5_realm = <domain name>
    realmd_tags = manages-system joined-with-adcli
    id_provider = ad
    fallback_homedir = /home/%u@%d
    ad_domain = <domain name>
    use_fully_qualified_names = True
    ldap_id_mapping = True
    access_provider = ad
    ldap_user_ssh_public_key = sshPublicKey


    [sudo]

    ```
    Then, restart sssd and test: `sudo systemctl sssd restart`

5. Configure sudo to allow sudo based on AD group: 
    ```
    visudo
    %linuxadmins@<domain name> ALL=(ALL) NOPASSWD:ALL
    ```

6. Store SSH Keys in AD. This is well explained by [this blog post](https://blog.laslabs.com/2016/08/storing-ssh-keys-in-active-directory/) and does need to be rehashed here. The value mapping I used is sshPublicKey, and this config line in sssd.conf is what you need:
    ```
    ldap_user_ssh_public_key = sshPublicKey
    ```
    Also, you'll need the following in `/etc/sshd/sshd_config`:
    ```
    AuthorizedKeysCommand /usr/bin/sss_ssh_authorizedkeys
    AuthorizedKeysCommandUser root
    ```
    Restart SSH server: `sudo systemctl sshd restart`. You can also test authorized keys are being pulled correctly with the following command: 
    ```
    sudo /usr/bin/sss_ssh_authorizedkeys <username>
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDex2RUYI75nlIPRyx1CPUVJrNE58qRVGJoUf+/Oq2xWBxAKjoksXYdyvCvNBijr56SBj7Zhze+LWUynlBD+Lp1afPtKft0qeltm7N7E9PKaIKUClxuSMnNJ/+Hr28k8+vFQfY/sKHzUstbykBh575IVQGwvXsEIsD70zKjkyHEGkytsZqDk+N35gSC68mya86+CaXvWCMn3njNmdeiYoUfQbOjdVCtfd7oB7mCZOfO9MIPFIEqiyHkXVePuBd0uOO9DjIO+WhPNeMHK9B7loOgA5foeGMeFS/7Hrp+FsX7zOeJcOqzBgogRhvEx63aEFziH+/dEfJlvI/OBVdEyDdOyIl7vAsi3rrB8uHN40vrFcfcOgeKHys3LwMu+rib75unvq6l0dGQlytz5ZITOKsmprXvc= 
    ```
    You may also need to clear the SSSD cache during the initial setup, `sudo sss_cache -E`


