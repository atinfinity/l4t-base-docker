FROM docker.io/arm64v8/ubuntu:24.04

ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES all

ARG UID=1000
ARG GID=1000

# add new sudo user
ENV USERNAME jetson
ENV HOME /home/$USERNAME

# https://askubuntu.com/questions/1513927/ubuntu-24-04-docker-images-now-includes-user-ubuntu-with-uid-gid-1000
RUN if getent passwd $UID; then \
      userdel -f $(getent passwd $UID | cut -d: -f1); \
    fi \
    && if getent group $GID; then \
      groupdel $(getent group $GID | cut -d: -f1); \
    fi

RUN useradd -m $USERNAME && \
        echo "$USERNAME:$USERNAME" | chpasswd && \
        usermod --shell /bin/bash $USERNAME && \
        usermod -aG sudo $USERNAME && \
        mkdir /etc/sudoers.d && \
        echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/$USERNAME && \
        chmod 0440 /etc/sudoers.d/$USERNAME && \
        usermod  --uid $UID $USERNAME && \
        groupmod --gid $GID $USERNAME
RUN gpasswd -a $USERNAME video

ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
    apt-get install -qq -y --no-install-recommends \
        bash-completion \
        bc \
        build-essential \
        bzip2 \
        can-utils \
        ca-certificates \
        cmake \
        command-not-found \
        curl \
        emacs \
        freeglut3-dev \
        git \
        gnupg2 \
        gstreamer1.0-alsa \
        gstreamer1.0-libav \
        gstreamer1.0-plugins-bad \
        gstreamer1.0-plugins-base \
        gstreamer1.0-plugins-good \
        gstreamer1.0-plugins-ugly \
        gstreamer1.0-tools \
        i2c-tools \
        iproute2 \
        iputils-ping \
        iw \
        kbd \
        kmod \
        language-pack-en-base \
        libapt-pkg-dev \
        libcanberra-gtk3-module \
        libgles2 \
        libglu1-mesa-dev \
        libglvnd-dev \
        libgtk-3-0 \
        libudev1 \
        libvulkan1 \
        libzmq5 \
        mesa-utils \
        mtd-utils \
        parted \
        pciutils \
        python3 \
        python3-numpy \
        python3-pexpect \
        python3-pip \
        sox \
        sudo \
        tmux \
        udev \
        vulkan-tools \
        wget \
        wireless-tools \
        wpasupplicant \
        && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# EGL
RUN echo "/usr/lib/aarch64-linux-gnu/tegra" >> /etc/ld.so.conf.d/nvidia-tegra.conf && \
    echo "/usr/lib/aarch64-linux-gnu/tegra-egl" >> /etc/ld.so.conf.d/nvidia-tegra.conf
RUN rm -rf /usr/share/glvnd/egl_vendor.d && \
    mkdir -p /usr/share/glvnd/egl_vendor.d/ && echo '\
{\
    "file_format_version" : "1.0.0",\
    "ICD" : {\
        "library_path" : "libEGL_nvidia.so.0"\
    }\
}' > /usr/share/glvnd/egl_vendor.d/10_nvidia.json
RUN mkdir -p /usr/share/egl/egl_external_platform.d/ && echo '\
{\
    "file_format_version" : "1.0.0",\
    "ICD" : {\
        "library_path" : "libnvidia-egl-wayland.so.1"\
    }\
}' > /usr/share/egl/egl_external_platform.d/nvidia_wayland.json

RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/jetson-ota-public.gpg] https://repo.download.nvidia.com/jetson/common r36.4 main" >> /etc/apt/sources.list.d/nvidia-l4t-apt-source.list && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/jetson-ota-public.gpg] https://repo.download.nvidia.com/jetson/t234 r36.4 main" >> /etc/apt/sources.list.d/nvidia-l4t-apt-source.list && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/jetson-ota-public.gpg] https://repo.download.nvidia.com/jetson/ffmpeg r36.4 main" >> /etc/apt/sources.list.d/nvidia-l4t-apt-source.list
RUN wget -O /etc/jetson-ota-public.key https://gitlab.com/nvidia/container-images/l4t-base/-/raw/master/jetson-ota-public.key && \
    cat /etc/jetson-ota-public.key | gpg --dearmor -o /usr/share/keyrings/jetson-ota-public.gpg

# CUDA, cuDNN
RUN apt-get update && \
    apt-get install -qq -y --no-install-recommends \
        cuda \
        libcudnn9 \
        libcudnn9-dev \
        libcudnn9-samples \
        && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN ldconfig

USER $USERNAME
WORKDIR /home/$USERNAME
SHELL ["/bin/bash", "-l", "-c"]

RUN echo "export PATH=/usr/local/cuda/bin:$PATH" >> ~/.bashrc && \
    echo "export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH" >> ~/.bashrc
