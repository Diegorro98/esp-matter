ARG ESP_IDF_TAG=v4.4.3
FROM espressif/idf:$ESP_IDF_TAG

ARG DEBIAN_FRONTEND=noninteractive

# Prerequisites for Matter
RUN : \
  && apt-get update \
  && apt-get install -y \
  git \
  gcc \
  g++ \
  pkg-config \
  libssl-dev \
  libdbus-1-dev \
  libglib2.0-dev \
  libavahi-client-dev \
  ninja-build \
  python3-venv \
  python3-dev \
  python3-pip \
  unzip \
  libgirepository1.0-dev \
  libcairo2-dev \
  libreadline-dev \
  && apt-get autoremove -y \
  && rm -rf /var/lib/apt/lists/* \
  && : # last line

# To build the image for a branch or a tag of ESP-MATTER, pass --build-arg ESP_MATTER_CLONE_BRANCH_OR_TAG=name.
# To build the image with a specific commit ID of ESP-MATTER, pass --build-arg ESP_MATTER_CHECKOUT_REF=commit-id.
# It is possibe to combine both, e.g.:
#   ESP_MATTER_CLONE_BRANCH_OR_TAG=release/vX.Y
#   ESP_MATTER_CHECKOUT_REF=<some commit on release/vX.Y branch>.
# Use ESP_MATTER_CLONE_SHALLOW=1 to peform shallow clone (i.e. --depth=1 --shallow-submodules)

ARG ESP_MATTER_CLONE_URL=https://github.com/espressif/esp-matter.git
ARG ESP_MATTER_CLONE_BRANCH_OR_TAG=main
ARG ESP_MATTER_CHECKOUT_REF=
ARG ESP_MATTER_CLONE_SHALLOW=

ENV ESP_MATTER_PATH=$IDF_TOOLS_PATH/esp-matter

RUN echo ESP_MATTER_CHECKOUT_REF=$ESP_MATTER_CHECKOUT_REF ESP_MATTER_CLONE_BRANCH_OR_TAG=$ESP_MATTER_CLONE_BRANCH_OR_TAG && \
    if [ -n "$ESP_MATTER_CLONE_SHALLOW" ]; then \
      git clone \
        ${ESP_MATTER_CLONE_BRANCH_OR_TAG:+-b $ESP_MATTER_CLONE_BRANCH_OR_TAG} \
        $ESP_MATTER_CLONE_URL $ESP_MATTER_PATH; \
    else \
      git clone --recursive \
        ${ESP_MATTER_CLONE_BRANCH_OR_TAG:+-b $ESP_MATTER_CLONE_BRANCH_OR_TAG} \
        $ESP_MATTER_CLONE_URL $ESP_MATTER_PATH; \
    fi && \
	  cd $ESP_MATTER_PATH && \
    if [ -n "$ESP_MATTER_CHECKOUT_REF" ]; then \
      git checkout $ESP_MATTER_CHECKOUT_REF; \
      if [ -n "$ESP_MATTER_CLONE_SHALLOW" ]; then \
        git fetch origin --depth=1 --recurse-submodules ${ESP_MATTER_CHECKOUT_REF}; \
      fi; \
    fi && \
    if [ -n "$ESP_MATTER_CLONE_SHALLOW" ]; then \
      git submodule update --init --depth=1 && \
      ./connectedhomeip/connectedhomeip/scripts/checkout_submodules.py --platform esp32 --shallow; \
    fi

RUN /bin/bash -c "source ${ESP_MATTER_PATH}/connectedhomeip/connectedhomeip/scripts/bootstrap.sh"

COPY entrypoint.sh /opt/esp/entrypoint.sh
ENTRYPOINT [ "/opt/esp/entrypoint.sh" ]
CMD [ "/bin/bash" ]
