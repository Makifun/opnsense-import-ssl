# opnsense-import-ssl

Script to import Let's Encrypt SSL certificates into a running OPNsense firewall.

**Features**

- Automatically import Let's Encrypt SSL Certificate.
- Automatically assigns the imported certificate to the GUI.
- Automatically check for and remove expired certificates (generated by the script).
- Automatically restart the web interface (for certificate to appear)

The script will automatically import the certificate and key file, generate an appropriate OPNsense certificate, import it to the live system, assign it to the web GUI; and save the configuration.

If any previously expired certificates (generated by the script) are detected, the script will clean them up and remove them from the system.

Newly imported certificates will have the description set to `Imported via opnsense-import-ssl on 2024-07-09`, for example. The `opnsense-import-ssl` defaults to the the script is saved as.

## Usage

**Important**: Ensure the script and the certificate and private key files are located in the same directory.

**Execute via PHP**

```
# php opnsense-import-ssl.php <certificate filename> <key filename> <domain string>
```

**Direct Script Exeution**

```
# chmod u+x ./opnsense-import-ssl.php
# ./opnsense-import-ssl.php <certificate filename> <key filename> <domain string>
```

**Example**

> [root@opnsense.example.com /root]# `./opnsense-import-ssl.php` `fullchain.pem` `privkey.pem` `example.com`


## Getting Started

Copy the script to your OPNsense firewall

Copy the `opnsense-import-ssl.php` script over to your OPNsense firewall. For example, using scp:

```
$ scp ./opnsense-import-ssl.php root@opnsense.example.com:/root/
```

### Using locally from OPNsense
---

If you would like to execute the scripts locally on your OPNsense firewall (for testing etc), ensure you have the script in the root directory; along with the Let's Encrypt `fullchain.pem` and `privkey.pem` certificate files.

To run the script, you have two choices.

**Execute via PHP**

```
[root@opnsense.example.com /root]# php opnsense-import-ssl.php fullchain.pem privkey.pem example.com
```

**Executable Script**

The script has the `#!/usr/bin/env php` shell envnironment at the beginning, it should be possible to execute the script directly:

```
[root@opnsense.example.com /root]# chmod +x opnsense-import-ssl.php
[root@opnsense.example.com /root]# ./opnsense-import-ssl.php fullchain.pem privkey.pem example.com
```

### Using remotely via SSH
---

**Preqrequisites**

Secure shell access must be fully configured to access your firewall from your remote system. 

- Enable SSH within the OPNsense GUI
- Enable root login (password or public key)

For security reasons it would be better to perform this from the LAN or via VPN. If you must expose SSH to the public internet, at the very least ensure it's restricted by IP address.

Setting up passwordless login would obviously be better from an automation point of view.

**Example SSH command from your remote system**

```
scp /usr/local/etc/letsencrypt/live/example.com/privkey.pem /usr/local/etc/letsencrypt/live/example.com/fullchain.pem root@example.com:/root/ && ssh root@example.com 'php opnsense-import-ssl.php /root/fullchain.pem /root/privkey.pem example.com'
```

## Sample Output

**First run:**

> Certificate imported.  
 Certificates updated successfully.

**Re-running the script with the same certificate and key files:**

> This certificate has already been imported.

**After certificate renewal:**

The script will automatically remove any old expired certificates after importing the new one:

> Certificate imported.  
expired Cert: Imported via importer on 2023-07-09  
1 of 3 certificates removed.  
Certificates updated successfully


