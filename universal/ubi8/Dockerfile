# syntax=docker/dockerfile:1.3-labs

# updateBaseImages.sh can't operate on SHA-based tags as they're not date-based or semver-sequential, and therefore cannot be ordered
FROM quay.io/devfile/base-developer-image:ubi8-latest
LABEL maintainer="Red Hat, Inc."

LABEL com.redhat.component="devfile-universal-container"
LABEL name="devfile/universal-developer-image"
LABEL version="ubi8"

#label for EULA
LABEL com.redhat.license_terms="https://www.redhat.com/en/about/red-hat-end-user-license-agreements#UBI"

#labels for container catalog
LABEL summary="devfile universal developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-universal"
LABEL io.openshift.expose-services=""

USER 10001

# Java
RUN curl -fsSL "https://get.sdkman.io" | bash \
    && bash -c ". /home/user/.sdkman/bin/sdkman-init.sh \
             && sed -i "s/sdkman_auto_answer=false/sdkman_auto_answer=true/g" /home/user/.sdkman/etc/config \
	     && sed -i "s/sdkman_auto_env=false/sdkman_auto_env=true/g" /home/user/.sdkman/etc/config \
             && sdk install java 11.0.15-tem \
             && sdk install java 17.0.3-tem \
             && sdk install java 22.1.0.0.r17-mandrel \
             && sdk default java 11.0.15-tem \
             && sdk install maven \
             && sdk install jbang \
             && sdk flush archives \
             && sdk flush temp"

# sdk home java <version>
ENV JAVA_HOME_11=/home/user/.sdkman/candidates/java/11.0.15-tem
ENV JAVA_HOME_17=/home/user/.sdkman/candidates/java/17.0.3-tem

# Java-related environment variables are described and set by /home/user/.bashrc
# To make Java working for dash and other shells, it needs to initialize them in the Dockerfile.
ENV SDKMAN_CANDIDATES_API="https://api.sdkman.io/2"
ENV SDKMAN_CANDIDATES_DIR="/home/user/.sdkman/candidates"
ENV SDKMAN_DIR="/home/user/.sdkman"
ENV SDKMAN_PLATFORM="linuxx64"
ENV SDKMAN_VERSION="5.13.0"

ENV JAVA_HOME="/home/user/.sdkman/candidates/java/current"
ENV MAVEN_HOME="/home/user/.sdkman/candidates/maven/current"

ENV GRAALVM_HOME=/home/user/.sdkman/candidates/java/22.1.0.0.r17-mandrel

ENV PATH="/home/user/.sdkman/candidates/maven/current/bin:$PATH"
ENV PATH="/home/user/.sdkman/candidates/java/current/bin:$PATH"

USER 0

# Required packages for AWT
RUN dnf install -y libXext libXrender libXtst libXi

# camel-k
ENV KAMEL_VERSION 1.10.1
RUN curl -L https://github.com/apache/camel-k/releases/download/v${KAMEL_VERSION}/camel-k-client-${KAMEL_VERSION}-linux-64bit.tar.gz | tar -C /usr/local/bin -xz \
    && chmod +x /usr/local/bin/kamel

# Cloud

# oc
ENV OC_VERSION=4.13.8
RUN curl -L https://mirror.openshift.com/pub/openshift-v4/clients/ocp/${OC_VERSION}/openshift-client-linux.tar.gz | tar -C /usr/local/bin -xz \
    && chmod +x /usr/local/bin/oc && chmod +x /usr/local/bin/kubectl

# Set permissions on /etc/passwd and /home to allow arbitrary users to write
RUN mkdir -p /home/user && chgrp -R 0 /home && chmod -R g=u /etc/passwd /etc/group /home

# cleanup dnf cache
RUN dnf -y clean all --enablerepo='*'

USER 10001
