---
title: Active Directory
date: 2026-02-24
categories: [project]
---

<ins><em>**Tool used**</em></ins>: 

- Active Directory

<ins><em>**Scope**</em></ins>: This documentation defines the step-by-step process for creating, modifying, disabling, and deleting user accounts in Active Directory (AD), creating and managing Group Policies, and adding both Windows and Linux computers to the AD domain using the organization’s standard domain-joining procedures

# <ins><em>**Process Description**</em></ins>:

## **a) User creation -**

i.Create the user account in Active Directory (AD) with the required details.

ii.Assign the user to the correct Organizational Unit (OU).

iii.Add the user to the appropriate security groups based on their role.

## **b) Group Policy Creation -**

i.Open the Group Policy Management Console (GPMC).

ii.Create a new Group Policy Object (GPO) with a suitable name.

iii.Configure the required settings inside the GPO (e.g. wallpaper lock).

iv.Link the GPO to the appropriate Organizational Unit (OU).

v.Update Group Policy on the target systems using gpupdate /force.

vi.Verify that the policy is applied successfully on user or computer systems.

## **c) Adding Windows and Linux Systems to Active Directory -**

### Windows 

i.Update network adapter settings to use the organization’s DNS server.

ii.Join the computer to the designated AD domain via system properties.

### Linux 

i.Execute the **join-ad.sh** script to join the computer to the AD domain.

<em><ins>**Note:**</ins></em> join-ad.sh code is given in process section. 

ii.The script applies required configurations and registers the system in AD.

# <ins>**High Level Process Diagram**<ins>

![](/assets/images/AD/Picture1.png)

![](/assets/images/AD/Picture2.png)

# <ins>**Process**</ins>

## **a) Creating User account**

<ins>**Step 1 :**</ins> Create the user account in Active Directory (AD) with the required details.

![](/assets/images/AD/2026-02-24_19-46.png)

![](/assets/images/AD/2026-02-24_19-47.png)

i. Open Active Directory Users and Computers (ADUC) from the Domain Controller 

ii. Right-click the OU and select New → User.

iii. Enter the required user information:

    I. First Name
    II. Last Name
    III. Initial
    IV. User Logon Name
    IV.Click Next to proceed.

v. Password can be set by the user

vi. Select the appropriate options such as password never expires

vii.Click Finish to create the user object in AD.

<ins>**Step 2 :**</ins> Assign the User to the Correct Organizational Unit (OU)

![](/assets/images/AD/2026-02-24_19-48.png)

i.Identify the correct OU based on department

ii.Right-click the newly created user object and select Move.

iii.Choose the appropriate OU from the directory tree.

iv.Confirm the move to ensure the user inherits the correct Group Policies (GPOs) and departmental configurations.

<ins>**Step 3 :**</ins> Add the user to the appropriate security groups based on their role.

![](/assets/images/AD/2026-02-24_19-50.png)


i.All new users are created as standard users with default permissions.

ii.Only authorized personnel (IT support) are granted access to critical systems.

## **b) Group Policy**

<ins>**Step 1 :**</ins> Open the Group Policy Management Console (GPMC).

![](/assets/images/AD/2026-02-24_19-59.png)

i.Log in to a domain-joined system with Administrative privileges.

ii.open it from Server Manager → Tools → Group Policy Management.

iii.Confirm that you can view the domain structure and existing GPOs.

<ins>**Step 2 :**</ins> Create a new Group Policy Object (GPO) with a suitable name.

![](/assets/images/AD/2026-02-24_20-00.png)

i.In the Group Policy Management Console, expand the domain.

ii.Right-click Group Policy Objects → New.

iii.Enter a meaningful name for the GPO (e.g., Wallpaper-Lock-Policy)

iv.Click OK to create the policy object.

<ins>**Step 3 :**</ins> Configure Required Settings in the GPO

![](/assets/images/AD/2026-02-24_20-01.png)

i.Right-click the newly created GPO → select Edit.

ii.The Group Policy Management Editor opens.

iii.Navigate to the required path depending on the policy type, for example:
```
User Configuration → Policies → Administrative Templates → Control panel → 	Personalization→ Prevent changing theme (for wallpaper lock)
```
iv.Enable the specific policy setting 

v.Apply and close the editor once configurations are complete.

<ins>**Step 4 :**</ins> Link the GPO to the Appropriate Organizational Unit (OU)

i.In GPMC, locate the target OU where the policy should apply.

ii.Right-click the OU → Link an Existing GPO.

iii.Select the GPO you created and click OK.

iv.Verify that the GPO is now listed under the OU’s Linked Group Policy Objects.

<ins>**Step 5 :**</ins> Verify Policy Application

i.Log in to a target system and check if the new policy is applied (e.g., wallpaper is locked).

ii.Use the command gpresult /r to check detailed policy results:

iii.Confirm the correct GPO shows under applied policies.

## **C) Adding Windows and Linux Systems to Active Directory**

Windows

![](/assets/images/AD/2026-02-24_20-28.png)

i. Open Control Panel → Network and Sharing Center → Change Adapter Settings, right-click the active connection, select Properties, disable IPv6, and set the IPv4 DNS to xxx.xxx.xx.xxx with an alternate of 8.8.8.8

![](/assets/images/AD/2026-02-24_20-29.png)

ii. Go to Settings → System → About → Advanced System Settings → Computer Name → Change, and update the domain name to join the AD domain.

Linux

![](/assets/images/AD/2026-02-24_20-33.png)

i. Ensure the join-ad.sh script is executable by running chmod +x join-ad.sh (the file will appear in green when executable), and then execute it using ./join-ad.sh to join the Linux system to the AD domain.

![](/assets/images/AD/2026-02-24_20-34.png)

ii. An Admin user prompt will appear, and you can enter the AD admin password to complete the domain join.

**join-ad.sh** : 

```bash

#!/bin/bash

# === CONFIGURATION ===
DOMAIN="" # Add your domain name
REALM=""  # Add your domain name
JOIN_USER="" # Add admin user name
AD_DNS=""  # Replace with your actual AD DNS IP

# === FUNCTION: Exit on error ===
error_exit() {
    echo -e "\n[✖] ERROR: $1"
    exit 1
}

log_info() {
    echo -e "\n[✔] $1"
}

# === 1. Check root ===
if [[ $EUID -ne 0 ]]; then
    error_exit "Run this script as root (sudo)."
fi

# === 2. Install required packages ===
log_info "Installing required packages..."
apt update -y || error_exit "apt update failed"
apt install -y realmd sssd sssd-tools samba-common packagekit adcli krb5-user \
    oddjob oddjob-mkhomedir libnss-sss libpam-sss || error_exit "Package installation failed"

# === 3. Configure DNS for AD resolution ===
log_info "Configuring AD DNS ($AD_DNS)..."
RESOLVED="/etc/systemd/resolved.conf"
if ! grep -q "^DNS=" "$RESOLVED"; then
    echo -e "\n[Resolve]\nDNS=$AD_DNS\nDomains=$DOMAIN" >> "$RESOLVED"
else
    sed -i "s/^DNS=.*/DNS=$AD_DNS/" "$RESOLVED"
    sed -i "s/^Domains=.*/Domains=$DOMAIN/" "$RESOLVED"
fi
systemctl restart systemd-resolved || error_exit "Failed to restart DNS service"

# === 4. Discover domain ===
log_info "Discovering AD domain..."
realm discover "$DOMAIN" || error_exit "Failed to discover AD domain"

# === 5. Join domain ===
log_info "Joining domain using user: $JOIN_USER"
realm join --user="$JOIN_USER" "$DOMAIN" || error_exit "Failed to join domain"

# === 6. Permit AD users (login only) ===
log_info "Allowing all AD users to log in (without sudo)..."
realm permit --all || error_exit "Failed to permit AD users"

# === 7. Enable home directory creation ===
log_info "Enabling home directory auto-creation for AD users..."
PAM_FILE="/etc/pam.d/common-session"
if ! grep -q "pam_mkhomedir.so" "$PAM_FILE"; then
    echo "session required pam_mkhomedir.so skel=/etc/skel/ umask=0077" >> "$PAM_FILE"
fi

systemctl enable oddjobd && systemctl start oddjobd || error_exit "Failed to enable/start oddjobd"

# === 8. Configure /etc/sssd/sssd.conf ===
log_info "Writing secure SSSD config..."
cat > /etc/sssd/sssd.conf <<EOF
[sssd]
domains = $DOMAIN
config_file_version = 2
services = nss, pam

[domain/$DOMAIN]
ad_domain = $DOMAIN
krb5_realm = $REALM
realmd_tags = manages-system joined-with-adcli
cache_credentials = True
id_provider = ad
access_provider = ad
ldap_id_mapping = True
default_shell = /bin/bash
override_homedir = /home/%u
use_fully_qualified_names = False
EOF

chmod 600 /etc/sssd/sssd.conf || error_exit "Failed to set permissions"
systemctl restart sssd || error_exit "Failed to restart SSSD"

# === 9. Validate ===
log_info "Verifying AD user resolution..."
id "$JOIN_USER" || error_exit "AD user not resolvable after join"

# === DONE ===
log_info "🎉 SUCCESS: System joined to $DOMAIN"
log_info "✔ Local + AD logins are allowed"
log_info "✔ Home directories will be created"
log_info "✔ No sudo access is granted to AD users"

```