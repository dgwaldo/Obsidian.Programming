## Expanding Memory
[Install Kibana with Docker | Kibana Guide [8.4] | Elastic](https://www.elastic.co/guide/en/kibana/current/docker.html) 

In order for Elastic to run correctly the WSL2 VM running the docker instances needs the max map count expanded.

When you're running it in WSL2, you can log into docker desktop with the following command:
`wsl -d docker-desktop` 
Where docker-desktop is the name of the vm. VM's can be listed with `wsl --list`

This command can be ran 
```
wsl -d docker-desktop
sysctl -w vm.max_map_count=262144
exit
```

Alternatively, the max map count can be set for all VM's by setting the .wslconfig globally. WSL config lives @ %userprofile%/.wslconfig [Advanced settings configuration in WSL | Microsoft Learn](https://learn.microsoft.com/en-us/windows/wsl/wsl-config)
Add the following lines to the .wslconfig file:
```
[wsl2]
memory=12GB

[wsl2]
kernelCommandLine = sysctl.vm.max_map_count=262144
```



