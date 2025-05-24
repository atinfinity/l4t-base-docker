# l4t-base-docker(L4T 36.4.0)

## Build docker image

```bash
docker build --build-arg UID=$(id -u) --build-arg GID=$(id -g) -t l4t-base:36.4.0 .
```

## Launch docker container

```bash
xhost +
docker run -it --rm --net=host --runtime nvidia -e DISPLAY=$DISPLAY -v /tmp/.X11-unix/:/tmp/.X11-unix l4t-base:36.4.0 bash
```
