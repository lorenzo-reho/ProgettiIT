# DOCKER

### COMANDI FONDAMENTALI:

- Per importare una image da docker-hub

```bash
sudo docker pull <image>:<version>
```

- Per visualizzare la lista delle image importate

```bash
sudo docker images
```

- Per visualizzare la lista dei container attivi

```bash
sudo docker ps
```

- Per runnare un container

```bash
sudo docker run --name <container_name> -d -i -t <image_name> bash
```

- Per visualizzare tutti  i container esistenti

```bash
sudo docker container ls -a
```

- Per eliminare un container:

```bash
sudo docker container rm <containerID>
```

- Per start/stop un container:

```bash
sudo docker start <containerID>
sudo docker stop <containerID>
```

- Per ottenere una shell interattiva su un running container

```bash
sudo docker exec <container_name> /bin/bash
```
