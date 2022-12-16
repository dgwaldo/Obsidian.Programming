## Expanding Memory

*Note:* So far have not found how to persist these settings.

*Before Installing: Expand memory in WSL for the VM*

WSL2: Log in to your WSL VM: 
```
wsl -d docker-desktop 
sysctl -w vm.max_map_count=262144
exit
```

(Where docker-desktop is the name of the vm, you can check for them with 
`wsl --list`

[Install Kibana with Docker | Kibana Guide [8.4] | Elastic](https://www.elastic.co/guide/en/kibana/current/docker.html) 



