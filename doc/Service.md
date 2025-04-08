# Linux Service

Because the `rMpp` has some quirks, we need to define the service file a certain way.

- No use of `/etc`
  This is due to the `/etc` being an overlay filesystem, which gets overridden on startup
- Wait for `/home/root`
  The `rMpp` only has a root user, the `/home` directory isn't immediately mounted, so we need to wait for that. 
- We need network access.
  No use in starting if no network service is running

## Proposal

`/etc/systemd/system/proton-drive-sync.service`

```service
[Unit]
Description=Proton Drive background sync
After=network.target
StartLimitIntervalSec=0
RequiresMountsFor=/home/root /var/lib/uboot

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/bin/bash /home/root/proton-drive-sync/start 
ExecStop=/bin/bash /home/root/proton-drive-sync/stop
TimeoutSec=infinity

[Install]
WantedBy=multi-user.target
```