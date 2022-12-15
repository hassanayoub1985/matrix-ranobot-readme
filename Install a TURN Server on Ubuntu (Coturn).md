# Installing a TURN Server on Ubuntu

You can install and configure a TURN server to use with Sametime Meeting on an Ubuntu operating system.

## Before you begin

You need the following:

- A Ubuntu server with a public IP-Address
- A custom domain name

## About this task

The following procedures uses Coturn open source implementation of a TURN server. The procedure includes installing and configuring the Conturn server.

## Procedure

1.  Install Coturn on the Ubuntu operating system.
    `       sudo apt-get -y update
     sudo apt-get -y install coturn`

         a. To start the Coturn Daemon at Startup, modify the /etc/default/coturn file.
             ```
             vim /etc/default/coturn
             ```

    b. Remove the comment character (#) from the beginning of the following line.
    `       TURNSERVER_ENABLED=1
   `

2.  Confige the Coturn server.
    a. Make a copy of your original configuration before making any changes.
    This original configuration copy can be used if a problem occurs.
    b. Create an empty file in the same directory to contain your configuration.
    ```
    sudo vim /etc/turnserver.conf
    ```
    c. Add the following content to define the Coturn server and replace the values with appropriate values for your configuration.

```
# Listener IP address of relay server. Multiple listeners can be specified.
# If no IP(s) specified in the config file or in the command line options,
# then all IPv4 and IPv6 system IPs will be used for listening.
listening-ip=0.0.0.0

# External IP-Address of the TURN server
external-ip=IP_ADDRESS

# TURN listener port for UDP and TCP (Default: 3478).
listening-port=3478

# 443 for TURN over TLS, which can bypass firewalls
tls-listening-port=443

# host domain name.
realm=ranobot.com

# 'Static' user accounts for the long term credentials mechanism, only.
# This option cannot be used with TURN REST API.
# 'Static' user accounts are NOT dynamically checked by the turnserver process,
# so they can NOT be changed while the turnserver is running.
#
#user=username1:key1
#user=username2:key2
# OR:
#user=username1:password1
#user=username2:password2
user=test:12345

# Path to the SSL certificate and private key.
# Certificate file.
cert=/etc/matrix-synapse/fullchain.pem

# Private key file.
pkey=/etc/matrix-synapse/privkey.pem

# Lower and upper bounds of the UDP relay endpoints:
# Further ports that are open for communication
min-port=10000
max-port=20000

# This allows TURN credentials to be accounted for a specific user id.
# If you don't have a suitable id, the timestamp alone can be used.
# This option is just turning on secret-based authentication.
# The actual value of the secret is defined by option static-auth-secret,
use-auth-secret

static-auth-secret=<YOUR_SECRET>

# Option to set the log file name.
# By default, the turnserver tries to open a log file in
# /var/log, /var/tmp, /tmp and current directories directories
log-file=/var/log/turnserver.log

# Enable verbose logging
verbose

# Do not allow an TLS/DTLS version of protocol
no-tlsv1
no-tlsv1_1
no-tlsv1_2
```

You can make additional customizations to the file. For additional information, see the turnserver.conf file. Within the file, configuration options are described as comments.

3. Save the file and restart the Coturn server to apply the changes.

## Sample Files
 - coturn
 - turnserver.conf