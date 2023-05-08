ARG VERSION=3.9-slim
FROM python:$VERSION

ARG COMMIT_SHA=main
ARG CREATED=""

# Fill in your labels as appropriate here
LABEL \
    org.opencontainers.image.created="$CREATED" \
    org.opencontainers.image.revision=$COMMIT_SHA \
    org.opencontainers.image.licenses=MIT \
    org.opencontainers.image.ref.name=wasabi \
    org.opencontainers.image.title="wasabi container" \
    org.opencontainers.image.description="wasabi built into a container"

# Set the SHELL option -o pipefail before RUN with a pipe in
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Installs google-chrome, chromedriver
RUN set -ex \
    && apt-get update --no-install-recommends -y \
    && apt-get install --no-install-recommends -y  \
       chromium \
       chromium-driver \
       gpg \
       tar \
       python3-tk \
    && rm -rf /var/lib/apt/lists/* \
    # arm64 driver location
	&& mkdir -p /usr/lib/chromium-browser/ \ 
	&& ln -s /usr/bin/chromedriver /usr/lib/chromium-browser/chromedriver

# Set display port as an environment variable
ENV DISPLAY=:99

# Create a user for wasabi
RUN useradd -m nonroot
RUN mkdir -p /home/nonroot
WORKDIR /home/nonroot
ENV PATH="/home/nonroot/.local/bin:${PATH}"

# Copy encrypted wasabi
#COPY wasabi.tar.gz.gpg .
COPY habanero/ .
RUN chmod +x ./wasabi.sh

# Now that the OS has been updated to include required packages, update ownership and then switch to nonroot user
RUN chown -R nonroot:nonroot /home/nonroot

USER nonroot
# Install additional Python requirements
RUN pip install --no-warn-script-location -r ./requirements.txt && rm ./requirements.txt

#ENTRYPOINT ["/bin/bash", "./wasabi.sh"]
CMD [ "./wasabi.sh" ]
