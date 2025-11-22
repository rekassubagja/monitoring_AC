FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

# Install base packages required for Flutter and Android SDK
RUN apt-get update && apt-get install -y \
    curl git unzip xz-utils zip wget ca-certificates gnupg2 build-essential \
    libglu1-mesa libgtk-3-0 libpulse0 libnss3 openjdk-17-jdk && \
  rm -rf /var/lib/apt/lists/*

# Android SDK root and PATH
ENV ANDROID_SDK_ROOT=/opt/android-sdk
ENV PATH=$PATH:/opt/flutter/bin:/opt/flutter/bin/cache/dart-sdk/bin:/opt/android-sdk/cmdline-tools/latest/bin:/opt/android-sdk/platform-tools

RUN mkdir -p /opt/android-sdk/cmdline-tools /opt/android-sdk/licenses /opt/android-sdk/platforms

# Download Android command line tools
RUN cd /tmp && \
    curl -fsSL https://dl.google.com/android/repository/commandlinetools-linux-1010_latest.zip -o cmdline-tools.zip && \
    unzip cmdline-tools.zip -d /opt/android-sdk/cmdline-tools && \
    rm cmdline-tools.zip || true

# Move extracted folder to 'latest' if necessary
RUN if [ -d /opt/android-sdk/cmdline-tools/cmdline-tools ]; then \
      mv /opt/android-sdk/cmdline-tools/cmdline-tools /opt/android-sdk/cmdline-tools/latest; \
    fi

# Install essential Android SDK components
RUN /opt/android-sdk/cmdline-tools/latest/bin/sdkmanager --sdk_root=${ANDROID_SDK_ROOT} --install "platform-tools" "platforms;android-33" "build-tools;33.0.2" || true

# Accept licenses (basic approach)
RUN mkdir -p /opt/android-sdk/licenses && echo "24333f8a63b6825ea9c5514f83c2829b004d1fee" > /opt/android-sdk/licenses/android-sdk-license || true

# Install Flutter (stable)
RUN git clone https://github.com/flutter/flutter.git /opt/flutter --branch stable --depth 1 || true

# Run flutter doctor once to populate cache (may fail harmlessly on some CI environments)
RUN /opt/flutter/bin/flutter doctor -v || true

# Create non-root user matching Codespaces' usual user
RUN useradd -ms /bin/bash vscode && chown -R vscode:vscode /opt && mkdir -p /workspace && chown -R vscode:vscode /workspace

USER vscode
WORKDIR /workspace

CMD ["/bin/bash"]
