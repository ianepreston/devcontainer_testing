ARG VARIANT=ubuntu
FROM mcr.microsoft.com/devcontainers/base:${VARIANT}

# Update the image
RUN apt update && apt upgrade -y

# Install updates and dependencies
RUN	apt update && \
	apt upgrade -y && \
	DEBIAN_FRONTEND=noninteractive apt install -y \
	# Required for ssh-agent (don't really need it in container but breaks my dotfiles otherwise)
	keychain \
	# dotfiles management
	rcm \
	# Handle timezone
	locales \
	# Gotta have vim
	vim

# Install starship prompt
RUN curl -sS https://starship.rs/install.sh  | sh -s -- --yes

# Need this because tzdata gets installed and prompts for location if you don't have it
ARG DEBIAN_FRONTEND=noninteractive
RUN ln -fs /usr/share/zoneinfo/America/Edmonton /etc/localtime
ENV TZ=America/Edmonton

RUN	DEBIAN_FRONTEND=noninteractive apt install -y \
	locales

# Set the locales for Canada and US (US needed for pyenv apparently)
RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen && \
	sed -i '/en_CA.UTF-8/s/^# //g' /etc/locale.gen && \
	locale-gen
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

# Do python specific stuff

# Pyenv dependencies: https://github.com/pyenv/pyenv/wiki#suggested-build-environment
RUN DEBIAN_FRONTEND=noninteractive apt install -y \
	make \
	build-essential \ 
	libssl-dev \
	zlib1g-dev \
	libbz2-dev \
	libreadline-dev \
	libsqlite3-dev \
	# wget \
	# curl \
	llvm \
	libncursesw5-dev \
	xz-utils \
	tk-dev \
	libxml2-dev \
	libxmlsec1-dev \
	libffi-dev \
	liblzma-dev 

# Switch to vscode user
USER vscode

# Setup pyenv
ARG PYENV_GIT_TAG=""
RUN curl https://pyenv.run | bash
RUN echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
RUN echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
# Will be overwritten by dotfiles probably but keep it to be safe
RUN echo 'eval "$(pyenv init -)"' >> ~/.bashrc
# Run everything once to let me use pyenv in the Dockerfile
ENV PYENV_ROOT /home/vscode/.pyenv
ENV PATH $PYENV_ROOT/bin:$PATH
ENV PATH $PYENV_ROOT/shims:$PATH

ARG PYTHON_1="3.10.7"
ARG PYTHON_2="3.10.7"
ARG PYTHON_3="3.10.7"
RUN pyenv install $PYTHON_1 && \
	pyenv install $PYTHON_2 && \
	pyenv install $PYTHON_3

RUN pyenv global $PYTHON_1 $PYTHON_2 $PYTHON_3

# Install Poetry
ARG POETRY_VERSION=""
# https://github.com/python-poetry/install.python-poetry.org#installation-layout
ENV DEB_PYTHON_INSTALL_LAYOUT deb
RUN curl -sSL https://install.python-poetry.org | python -

# Install pipx
RUN python3 -m pip install --user pipx && python3 -m pipx ensurepath
# I think pipx does this for bash and docker runs sh so we have to add it manually
ENV PATH home/vscode/.local/bin:$PATH

# Install nox
RUN pipx install nox && pipx inject nox nox-poetry && pipx install cruft && pipx install cookiecutter
