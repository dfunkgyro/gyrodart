# Base Alpine image
FROM alpine:3.20

# Install base tools
RUN apk update && apk add --no-cache \
    bash curl git unzip xz zip \
    openjdk17 \
    libc6-compat \
    libstdc++ \
    zlib \
    wget tar \
    mesa mesa-dri-swrast mesa-gl \
    libx11 libxcomposite libxrandr libxi \
    libxrender libxtst libnss \
    fontconfig ttf-dejavu

# Set Java home
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk
ENV PATH="$JAVA_HOME/bin:$PATH"

# Install Flutter
ENV FLUTTER_VERSION=3.24.3
RUN git clone https://github.com/flutter/flutter.git /usr/local/flutter -b stable --depth 1 \
    && /usr/local/flutter/bin/flutter --version
ENV PATH="/usr/local/flutter/bin:/usr/local/flutter/bin/cache/dart-sdk/bin:${PATH}"

# Enable Flutter web & Linux desktop
RUN flutter config --enable-web \
    && flutter config --enable-linux-desktop

# Install Android SDK command-line tools
ENV ANDROID_SDK_ROOT=/usr/local/android-sdk
RUN mkdir -p ${ANDROID_SDK_ROOT}/cmdline-tools \
    && wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip -O /tmp/cmdline-tools.zip \
    && unzip -q /tmp/cmdline-tools.zip -d ${ANDROID_SDK_ROOT}/cmdline-tools \
    && mv ${ANDROID_SDK_ROOT}/cmdline-tools/cmdline-tools ${ANDROID_SDK_ROOT}/cmdline-tools/latest \
    && rm /tmp/cmdline-tools.zip

ENV PATH="${ANDROID_SDK_ROOT}/cmdline-tools/latest/bin:${ANDROID_SDK_ROOT}/platform-tools:${ANDROID_SDK_ROOT}/emulator:${PATH}"

# Accept Android licenses and install SDK packages
RUN yes | sdkmanager --licenses \
    && sdkmanager "platform-tools" \
    "platforms;android-34" \
    "build-tools;34.0.0" \
    "patcher;v4" \
    "emulator" \
    "system-images;android-30;google_apis;x86_64"

# Create an Android Virtual Device (AVD)
RUN echo "no" | avdmanager create avd \
    --name flutter_emulator \
    --package "system-images;android-30;google_apis;x86_64" \
    --device "pixel"

# Pre-cache Flutter
RUN flutter precache

# Create a non-root user
RUN addgroup -S vscode && adduser -S vscode -G vscode
USER vscode
WORKDIR /workspaces
