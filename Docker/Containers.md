
### Starting Containers Automatically
Set Docker Desktop up to automatically start, then set the container to autostart.

[Start containers automatically | Docker Documentation](https://docs.docker.com/config/containers/start-containers-automatically/)

exp: `docker update --restart=always es-node01`


NOTE: The following might be working for persisting this between restarts.

Please use this commands `wsl -d docker-desktop` `echo 262144 >> /proc/sys/vm/max_map_count` 

– [Josh](https://stackoverflow.com/users/1444061/josh "809 reputation")

 [Apr 7, 2021 at 16:14](https://stackoverflow.com/questions/42111566/elasticsearch-in-windows-docker-image-vm-max-map-count#comment118413449_62078093)