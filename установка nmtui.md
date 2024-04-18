```
apt install network-manager
```
```
nano /etc/NetworkManager/NetworkManager.conf
```

[ifupdown]
managed=true - замена на тру с фолс

systemctl restart NetworkManager.service
