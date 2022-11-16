## Expanding Memmory

*Note:* So far have not found how to persist these settings.

*Before Installing: Expand memory in WSL for the VM*

```
wsl.exe -u root
sysctl -w vm.max_map_count=262144
exit
```

When you're running it in WSL2, you might want to log in to your WSL VM: `wsl -d docker-desktop` (Where docker-desktop is the name of the vm, you can check for them with `wsl --list`

Once in your docker-desktop, do the following:

`echo "vm.max_map_count = 262144"> /etc/sysctl.d/999-docker-desktop-conf`

followed by: `sysctl -w vm.max_map_count=262144`

You can then exit the docker-host by typing `exit`

[Install Kibana with Docker | Kibana Guide [8.4] | Elastic](https://www.elastic.co/guide/en/kibana/current/docker.html) 



