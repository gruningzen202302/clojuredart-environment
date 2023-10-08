# Use the official Ubuntu base image
FROM ubuntu:latest

# Set environment variables
ENV FLUTTER_HOME="/flutter"
ENV DART_HOME="/flutter/bin/cache/dart-sdk"
ENV JAVA_HOME="/usr/lib/jvm/temurin-21"
ENV CLOJURE_VERSION="1.10.3.967"

# Datomic-specific environment variables
ENV DATOMIC_HOME="/datomic"
ENV DATOMIC_VERSION="0.9.6182"
ENV DATOMIC_DATA_DIR="/datomic-data"

# Install prerequisites and utilities
RUN apt-get update && apt-get install -y \
    unzip \
    git \
    curl \
    wget \
    openjdk-11-jre-headless  # Use OpenJDK 11 for Datomic

# Install Flutter and Dart
RUN git clone https://github.com/flutter/flutter.git ${FLUTTER_HOME} \
    && ${FLUTTER_HOME}/bin/flutter doctor

# Install Temurin (AdoptOpenJDK) JVM 21
RUN wget -qO - https://adoptium.net/api/gpg/key/public | apt-key add - \
    && echo "deb https://adoptium.net/archive/deb/ubuntu focal main" | tee /etc/apt/sources.list.d/adoptium.list \
    && apt-get update && apt-get install -y adoptium-21-hotspot-jre \
    && update-java-alternatives -s adoptium-21-hotspot-jre

# Install Clojure
RUN curl -O https://download.clojure.org/install/linux-install-${CLOJURE_VERSION}.sh \
    && chmod +x linux-install-${CLOJURE_VERSION}.sh \
    && ./linux-install-${CLOJURE_VERSION}.sh \
    && rm linux-install-${CLOJURE_VERSION}.sh

# Download and set up Datomic
RUN mkdir -p ${DATOMIC_HOME} ${DATOMIC_DATA_DIR} \
    && wget -q https://my.datomic.com/downloads/free/${DATOMIC_VERSION} -O ${DATOMIC_HOME}/datomic-free-${DATOMIC_VERSION}.zip \
    && unzip ${DATOMIC_HOME}/datomic-free-${DATOMIC_VERSION}.zip -d ${DATOMIC_HOME} \
    && rm ${DATOMIC_HOME}/datomic-free-${DATOMIC_VERSION}.zip

# Add Datomic and Clojure binaries to the PATH
ENV PATH="${FLUTTER_HOME}/bin:${DART_HOME}:${JAVA_HOME}/bin:${DATOMIC_HOME}/bin:${PATH}"

# Set the Flutter SDK path
ENV FLUTTER_SDK="${FLUTTER_HOME}"

# Cleanup
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Set the working directory
WORKDIR /app

# Start a shell session by default
CMD ["bash"]
