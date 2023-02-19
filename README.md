# pihole
Docker compose file and env templates for self-hosted Pi-hole instance
## Deployment
```
# systemctl disable --now  systemd-resolved
git clone git@github.com:aryzhk0v/pihole.git && cd pihole
docker compose up -d
```
## Setup

Unfortunately my router doesn't allow changing DNS servers and for some reason neither Pi-hole nor `dhcpd` respond to DHCP Discover requests so I had to set up DNS server address manually
### Linux:
```
mv /etc/resolv.conf{,-bk}
nmcli con ## get connection name
# nmcli con mod "CONNECTION NAME" ipv4.dns "PIHOLE IP"
# systemctl restart NetworkManager
```
Verify that DNS were changed:
```
cat /etc/resolv.conf
```
## Backups
Backups are saved into a host directory specified in `docker-composes.yml` :
```
    volumes:
        - '~/backups/pihole:/mnt'
```
using built-in `pihole` command which can be run regularly via cronjob:
```
24 1 * * * username docker exec -t pihole bash -c 'cd /mnt && pihole -a -t'
```
## Restore
Web-admin interface -> Settings -> Teleporter ->  Restore
## Notes
- If a Windows client is set up to use pihole but `nslookup` shows that it queries `fe80::1` server, try to disable IPv6 networking