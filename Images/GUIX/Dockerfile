# Use the official Guix base image
FROM guix/guix:latest

# Set environment variables
ENV FLUTTER_HOME="/flutter"
ENV DART_HOME="/flutter/bin/cache/dart-sdk"
ENV JAVA_HOME="/usr/lib/jvm/default-jvm"
ENV CLOJURE_VERSION="1.10.3.967"

# Datomic-specific environment variables
ENV DATOMIC_HOME="/datomic"
ENV DATOMIC_VERSION="0.9.6182"
ENV DATOMIC_DATA_DIR="/datomic-data"

# Install prerequisites
RUN guix pull --max-derivations=0  # Ensure the latest Guix profiles are available
RUN guix package -i unzip git curl wget openjdk@11  # Install necessary packages

# Install Flutter and Dart
RUN git clone https://github.com/flutter/flutter.git ${FLUTTER_HOME} \
    && ${FLUTTER_HOME}/bin/flutter doctor

# Install Clojure
RUN guix install clojure@${CLOJURE_VERSION}

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
RUN guix package --delete-generations=3  # Remove unnecessary package generations
RUN rm -rf /var/guix/profiles/per-user/root/guix-profile  # Remove unused Guix profile

# Set the working directory
WORKDIR /app

# Start a shell session by default
CMD ["bash"]
