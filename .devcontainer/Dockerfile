FROM mcr.microsoft.com/vscode/devcontainers/base:ubuntu-22.04

ARG USERNAME=vscode
ENV DEBIAN_FRONTEND noninteractive

ENV DISPLAY :0
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US.UTF-8 \
    LC_ALL=C.UTF-8 


ARG GHC_VERSION=9.0.2
ARG STACK_VERSION=2.7.5
ARG STACK_RESOLVER=lts-19.16
ARG CABAL_VERSION=3.4.1.0
ARG HLS_VERSION=1.7.0.0
ARG LLVM_VERSION=12

ENV GHC_VERSION=${GHC_VERSION} \
    STACK_RESOLVER=${STACK_RESOLVER} \
    STACK_VERSION=${STACK_VERSION} \
    CABAL_VERSION=${CABAL_VERSION} \
    HLS_VERSION=${HLS_VERSION} \
    LLVM_VERSION=${LLVM_VERSION}


# C++
# python
# node
# SDL2
# OpenGL
# Qt5
# GTK4
RUN apt-get update && apt-get install -y --no-install-recommends build-essential \
    git unzip dosfstools curl wget sudo ssh tzdata ca-certificates \
    dbus dbus-x11 alsa-utils pulseaudio pulseaudio-utils mesa-utils x11-apps \
    libc6-dev \
    libffi-dev \
    libgmp-dev \
    libnuma-dev \
    libtinfo-dev \
    zlib1g-dev \
    # cmake clang clang-format clangd gdb nasm lldb\ 
    # python3 python-is-python3 python3-distutils python3-numpy python3-pip python3-tk \
    # nodejs \
    # libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-net-dev libsdl2-ttf-dev \
    # libglu1-mesa-dev freeglut3-dev mesa-common-dev libglew-dev libglfw3-dev \
    # qtcreator qtbase5-dev qttools5-dev-tools qtbase5-examples qt5-qmake \
    # libgtk-4-bin libgtk-4-common libgtk-4-dev \
    && apt-get clean -y && rm -rf /var/lib/apt/lists



USER ${USERNAME}

# Install Rust
# ENV RUST_VERSION stable
# RUN curl https://sh.rustup.rs -sSf | sh -s -- -y --default-toolchain ${RUST_VERSION}
# ENV PATH $PATH:/home/${USERNAME}/.cargo/bin
# RUN rustup install stable
# RUN rustup install nightly
# RUN rustup component add rust-analysis rust-src rustfmt clippy
# RUN cargo install cargo-watch
# RUN cargo install cargo-check

RUN mkdir -p /home/${USERNAME}/workspace
WORKDIR /home/${USERNAME}/workspace


RUN mkdir -p "/home/vscode/.ghcup/bin" \
    && curl -LJ "https://downloads.haskell.org/~ghcup/x86_64-linux-ghcup" -o "/home/vscode/.ghcup/bin/ghcup" \
    && chmod +x "/home/vscode/.ghcup/bin/ghcup"
ENV PATH="/home/vscode/.cabal/bin:/home/vscode/.ghcup/bin:$PATH"


# [Choice] GHC version: recommended, latest, 9.2, 9.0, 8.10, 8.8, 8.6
ARG GHC_VERSION="9.0"
RUN ghcup install ghc "${GHC_VERSION}" --set \
    && ghcup install cabal recommended --set \
    && ghcup install stack recommended --set \
    && ghcup install hls recommended --set \
    && cabal update

RUN cabal install \
    fsnotify-0.3.0.1 \
    haskell-dap-0.0.15.0 \
    ghci-dap-0.0.17.0 \
    haskell-debug-adapter-0.0.35.0 \
    hlint-3.3.6 \
    apply-refact-0.9.3.0 \
    retrie-1.1.0.0 \
    ormolu-0.3.1.0


USER root
COPY set_uid.sh /home/
ENTRYPOINT ["/home/set_uid.sh"]
COPY ./.inject_bashrc /root
RUN cat /root/.inject_bashrc >> /home/${USERNAME}/.bashrc
CMD ["/bin/sh", "-c", "echo Container started ; trap \"exit 0\" 15; while sleep 1 & wait $!; do :; done"]
