# Running dnscrypt-proxy on a Linux Laptop

Download the appropriate release, usually Linux x86_64 

[https://github.com/DNSCrypt/dnscrypt-proxy/releases/](https://github.com/DNSCrypt/dnscrypt-proxy/releases)

Once you have the files, untar in a directory. 

Next we need to edit the `dnscrypt-proxy.toml` file

## Editing the configuration

> Use the `.toml` file provided

Choose appropriate `server_names`

Set the  `listen_addresses = ["127.0.0.53:53"]`

Set a `fallback_resolver` just in case

Ensure `cache = true` 

## Configure a service and replace the systemd-resolve

These are the steps that allowed me to move completely replacing DNSCrypt over systemd-resolve

1. For many reasons I didn't want to disable systemd-resolv so first thing was to just stop it from listening on `127.0.0.53 port 53`.
2. This change can be done in `/etc/systemd/resolved.conf` I changed `DNSStubListener=yes` to `DNSStubListener=no`
3. Now in dnscrypt config `dnscrypt-proxy.toml` I added the listener `listen_addresses = ["127.0.0.53:53"]`
4. Now If dnscrypt-proxy is running we have a local DNS (caching) which can talk to DNSCrypt servers. I use captnemo-in run by [@captnemo twitter](https://twitter.com/captn3m0) with low latency for me. Additionally I have nextdns as a back up here
5. If everything is working fine, all we now need to do is add this as a service so that we can start/stop it at will and save it so that it can survive a reboot

> Use the `.service` file provided

```bash
sudo touch /etc/systemd/system/dnscrypt.service
chmod 664 /etc/systemd/system/dnscrypt.service
sudo systemctl start dnscrypt.service
sudo systemctl status dnscrypt.service
# If everything is fine
sudo systemctl enable dnscrypt.service

# In case you want to stop it from loading at boot
sudo systemctl disable dnscrypt.service
```
