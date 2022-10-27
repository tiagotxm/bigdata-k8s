FROM openjdk:8-slim AS hadoop-layer
# SOURCE - https://github.com/uprush/kube-spark/blob/master/docker/hadoop.Dockerfile

# Variables that define which software versions to install.
ARG HADOOP_VERSION=3.2.1
ENV JAVA_HOME=/usr/local/openjdk-8

# Install dependencies
RUN apt update && apt install -y curl --no-install-recommends \
    && apt-get --no-install-recommends autoremove -yqq --purge \
    && apt-get clean \
    && rm -rf \
        /var/lib/apt/lists/* \
        /tmp/* \
        /var/tmp/* \
        /usr/share/man \
        /usr/share/doc \
        /usr/share/doc-base

# Download and extract the Hadoop binary package.
RUN curl https://archive.apache.org/dist/hadoop/core/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz \
	| tar xz -C /opt/  \
	&& ln -s /opt/hadoop-$HADOOP_VERSION /opt/hadoop \
	&& rm -r /opt/hadoop/share/doc

ARG AWS_SDK_VERSION=1.11.788
RUN rm /opt/hadoop/share/hadoop/tools/lib/aws-java-sdk-bundle-*.jar \
    && curl https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-bundle/${AWS_SDK_VERSION}/aws-java-sdk-bundle-${AWS_SDK_VERSION}.jar \
	 --output /opt/hadoop/share/hadoop/tools/lib/aws-java-sdk-bundle-${AWS_SDK_VERSION}.jar

# Add S3a jars to the classpath using this hack.
RUN ln -s /opt/hadoop/share/hadoop/tools/lib/hadoop-aws* /opt/hadoop/share/hadoop/common/lib/ && \
    ln -s /opt/hadoop/share/hadoop/tools/lib/aws-java-sdk* /opt/hadoop/share/hadoop/common/lib/

# Add 'hadoop' user so that this cluster is not run as root.
RUN groupadd -g 1010 hadoop && \
    useradd -r -m -u 1010 -g hadoop hadoop && \
    mkdir -p /opt/hadoop/logs && \
    chown -R -L hadoop /opt/hadoop && \
    chgrp -R -L hadoop /opt/hadoop

# Set necessary environment variables.
ENV HADOOP_HOME="/opt/hadoop"
ENV PATH="/opt/hadoop/bin:${PATH}"