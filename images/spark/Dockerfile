ARG HADOOP_VERSION=3.2.1

FROM tiagotxm/hadoop:v${HADOOP_VERSION} AS spark-layer

# Variables that define which software versions to install.
ARG SCALA_MAIN_VER=2.12
ARG SCALA_VER=${SCALA_MAIN_VER}.10
ARG SPARK_VERSION=3.2.1
ARG PACKAGE=spark-$SPARK_VERSION-bin-without-hadoop

# Install
RUN apt update \
    && apt install -y curl tini libc6 libpam-modules libnss3 \
    && apt install -y --no-install-recommends equivs \
    && apt-get autoremove -yqq --purge \
    && apt-get clean \
    && rm -rf \
        /var/lib/apt/lists/* \
        /tmp/* \
        /var/tmp/* \
        /usr/share/man \
        /usr/share/doc \
        /usr/share/doc-base

# Install fake java8-runtime-headless (scala 2.11 dependency) in order to install Scala
COPY java8-runtime-headless.control /tmp
RUN cd /tmp && equivs-build /tmp/java8-runtime-headless.control \
    && dpkg -i java8-runtime-headless_8u242_all.deb \
    && rm java8-runtime-headless.control \
    && rm java8-runtime-headless_8u242_all.deb

# Download and install Scala.
RUN curl -O https://www.scala-lang.org/files/archive/scala-$SCALA_VER.deb \
	&& dpkg -i scala-$SCALA_VER.deb \
	&& rm scala-$SCALA_VER.deb

# Download and install Spark.
RUN curl https://archive.apache.org/dist/spark/spark-$SPARK_VERSION/$PACKAGE.tgz \
	| tar -xz -C /opt/ \
	&& ln -s /opt/$PACKAGE /opt/spark

# Add 'spark' user so that this cluster is not run as root.
RUN groupadd -g 1080 spark && \
    useradd -r -m -u 1080 -g spark spark && \
    chown -R -L spark /opt/spark && \
    chgrp -R -L spark /opt/spark

# Set necessary environment variables.
ENV SPARK_HOME="/opt/spark"
ENV PATH="/opt/spark/bin:${PATH}"
RUN echo "export SPARK_DIST_CLASSPATH=$(hadoop classpath)" >> /opt/spark/conf/spark-env.sh

COPY log4j.properties /opt/spark/conf/
COPY spark-entrypoint.sh /opt/entrypoint.sh
ENTRYPOINT ["/opt/entrypoint.sh"]