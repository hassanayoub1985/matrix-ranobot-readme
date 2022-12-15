# Installing Synapse
## Prebuilt packages
Prebuilt packages are available for a number of platforms. These are recommended for most users.

## Docker images and Ansible playbooks
There is an official synapse image available at https://hub.docker.com/r/matrixdotorg/synapse which can be used with the docker-compose file available at contrib/docker. Further information on this including configuration options is available in the README on hub.docker.com.

Alternatively, Andreas Peters (previously Silvio Fricke) has contributed a Dockerfile to automate a synapse server in a single Docker image, at https://hub.docker.com/r/avhost/docker-matrix/tags/

Slavi Pantaleev has created an Ansible playbook, which installs the offical Docker image of Matrix Synapse along with many other Matrix-related services (Postgres database, Element, coturn, ma1sd, SSL support, etc.). For more details, see https://github.com/spantaleev/matrix-docker-ansible-deploy

## Debian/Ubuntu
### Matrix.org packages
Matrix.org provides Debian/Ubuntu packages of Synapse, for the amd64 architecture via https://packages.matrix.org/debian/.

To install the latest release:

```
sudo apt install -y lsb-release wget apt-transport-https
sudo wget -O /usr/share/keyrings/matrix-org-archive-keyring.gpg https://packages.matrix.org/debian/matrix-org-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/matrix-org-archive-keyring.gpg] https://packages.matrix.org/debian/ $(lsb_release -cs) main" |
    sudo tee /etc/apt/sources.list.d/matrix-org.list
sudo apt update
sudo apt install matrix-synapse-py3
```
Packages are also published for release candidates. To enable the prerelease channel, add `prerelease` to the `sources.list` line. For example:

```
sudo wget -O /usr/share/keyrings/matrix-org-archive-keyring.gpg https://packages.matrix.org/debian/matrix-org-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/matrix-org-archive-keyring.gpg] https://packages.matrix.org/debian/ $(lsb_release -cs) main prerelease" |
    sudo tee /etc/apt/sources.list.d/matrix-org.list
sudo apt update
sudo apt install matrix-synapse-py3
```
The fingerprint of the repository signing key (as shown by `gpg /usr/share/keyrings/matrix-org-archive-keyring.gpg`) is `AAF9AE843A7584B5A3E4CD2BCF45A512DE2DA058`.

When installing with Debian packages, you might prefer to place files in `/etc/matrix-synapse/conf.d/` to override your configuration without editing the main configuration file at `/etc/matrix-synapse/homeserver.yaml`. By doing that, you won't be asked if you want to replace your configuration file when you upgrade the Debian package to a later version.
`homeserver.yaml` [more details](https://matrix-org.github.io/synapse/latest/usage/configuration/config_documentation.html)
```
pid_file: "/var/run/matrix-synapse.pid"
listeners:
  - port: 52222
    tls: true
    type: http
    x_forwarded: true
    bind_addresses: ['::1', '127.0.0.1', '144.126.221.12']
    resources:
      - names: [client, federation]
        compress: false
database:
  name: sqlite3
  args:
    database: /var/lib/matrix-synapse/homeserver.db
log_config: "/etc/matrix-synapse/log.yaml"
media_store_path: /var/lib/matrix-synapse/media
signing_key_path: "/etc/matrix-synapse/homeserver.signing.key"
trusted_key_servers: []
tls_certificate_path: "/etc/matrix-synapse/fullchain.pem"
tls_private_key_path: "/etc/matrix-synapse/privkey.key"
max_upload_size: 102400M
turn_uris: [ "turn:ranobot.com?transport=udp", "turn:ranobot.com?transport=tcp" ]
turn_user_lifetime: 86400000
turn_allow_guests: true
```
For https connection
```
...
tls: true
...
tls_certificate_path: "/etc/matrix-synapse/fullchain.pem"
tls_private_key_path: "/etc/matrix-synapse/privkey.key"
...
```

For limitation upload file size
```
...
max_upload_size: 102400M
...
```

For TurnServer
```
...
turn_uris: [ "turn:ranobot.com?transport=udp", "turn:ranobot.com?transport=tcp" ]
turn_user_lifetime: 86400000
turn_allow_guests: true
```

### The Sample File
 - homeserver.yaml

### Neccessary Files
 - homeserver.yaml
 - fullchain.pem
 - privkey.key or privkey.pem