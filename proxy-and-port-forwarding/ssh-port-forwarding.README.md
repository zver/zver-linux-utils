
Tests connectivity and throughput to a list of SSH jump hosts, selects the
best-performing host, and creates a local SSH port-forwarding tunnel to the
target server through the selected jump host. If SSH_JUMP_HOSTS is set to an
empty value, the tunnel is established directly to the target server without
using a jump host.

# Example config file /etc/ssh-port-forwarding

```
SSH_JUMP_KEY_FILE=/etc/user2_key
SSH_JUMP_USER=user2
SSH_JUMP_HOSTS="192.168.10.1,192.168.10.2"

SSH_KEY_FILE=/etc/tunnel_key
SSH_USER=tunnel
SSH_HOST=1.1.1.1

LOCAL_PORT=12080
REMOTE_PORT=2080
```

# Add user

```
sudo useradd \
    --create-home \
    --shell /usr/sbin/nologin \
    tunnel
sudo -u tunnel ssh-keygen -t ed25519
cat /home/tunnel/.ssh/id_ed25519.pub \
    | sudo -u tunnel tee /home/tunnel/.ssh/authorized_keys \
    >/dev/null
```


# Limit user rights in sshd config (/etc/ssh/sshd_config) on $SSH_HOST

```
Match User tunnel
    PasswordAuthentication no
    PermitTTY no
    X11Forwarding no
    AllowAgentForwarding no
    PermitTunnel no
    AllowTcpForwarding local
    PermitOpen 127.0.0.1:2080
```

