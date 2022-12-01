ARG VARIANT=ubuntu
FROM mcr.microsoft.com/devcontainers/base:${VARIANT}

# Update the image
RUN apt update && apt upgrade -y

# Install dotfile dependencies
RUN	DEBIAN_FRONTEND=noninteractive apt install -y \
	rcm

# Install starship prompt
RUN curl -sS https://starship.rs/install.sh  | sh -s -- --yes