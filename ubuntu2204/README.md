# l4t-base-docker(L4T 35.3.1)

## Build docker image

```bash
docker build --build-arg UID=$(id -u) --build-arg GID=$(id -g) -t l4t-base:35.3.1 .
```

## Launch docker container

```bash
xhost +
docker run -it --rm --net=host --runtime nvidia -e DISPLAY=$DISPLAY -v /tmp/.X11-unix/:/tmp/.X11-unix l4t-base:35.3.1 bash
```
