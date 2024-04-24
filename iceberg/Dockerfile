FROM python:3.9-bullseye

# Install necessary packages
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    sudo \
    curl \
    nano \
    unzip \
    openjdk-11-jdk \
    build-essential \
    software-properties-common \
    ssh && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install Jupyter and other Python dependencies
COPY requirements.txt .
RUN pip3 install -r requirements.txt

# Add scala kernel via spylon-kernel
RUN python3 -m spylon_kernel install

# Download and install IJava Jupyter kernel
RUN curl https://github.com/SpencerPark/IJava/releases/download/v1.3.0/ijava-1.3.0.zip -Lo ijava-1.3.0.zip \
  && unzip ijava-1.3.0.zip \
  && python3 install.py --sys-prefix \
  && rm ijava-1.3.0.zip

# Set environment variables
ENV SPARK_HOME="/opt/spark"
ENV SPARK_VERSION="3.5.1"
ENV SPARK_MAJOR_VERSION="3.5"
ENV ICEBERG_VERSION="1.5.0"
ENV PATH="/opt/spark/sbin:/opt/spark/bin:${PATH}"

# Download Spark
RUN mkdir -p ${SPARK_HOME} \
    && curl https://dlcdn.apache.org/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop3.tgz -o spark-${SPARK_VERSION}-bin-hadoop3.tgz \
    && tar xvzf spark-${SPARK_VERSION}-bin-hadoop3.tgz --directory /opt/spark --strip-components 1 \
    && rm -rf spark-${SPARK_VERSION}-bin-hadoop3.tgz

# Download iceberg Spark runtime
RUN curl https://repo1.maven.org/maven2/org/apache/iceberg/iceberg-spark-runtime-${SPARK_MAJOR_VERSION}_2.12/${ICEBERG_VERSION}/iceberg-spark-runtime-${SPARK_MAJOR_VERSION}_2.12-${ICEBERG_VERSION}.jar -Lo /opt/spark/jars/iceberg-spark-runtime-${SPARK_MAJOR_VERSION}_2.12-${ICEBERG_VERSION}.jar

# Download AWS bundle
RUN curl -s https://repo1.maven.org/maven2/org/apache/iceberg/iceberg-aws-bundle/${ICEBERG_VERSION}/iceberg-aws-bundle-${ICEBERG_VERSION}.jar -Lo /opt/spark/jars/iceberg-aws-bundle-${ICEBERG_VERSION}.jar

# Install AWS CLI
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip \
    && ./aws/install \
    && rm awscliv2.zip \
    && rm -rf aws/

# Set IJava classpath
ENV IJAVA_CLASSPATH="/opt/spark/jars/*"

# Copy data and notebooks
RUN mkdir -p /home/iceberg/data /home/iceberg/notebooks /home/iceberg/spark-events
COPY /climate_change.parquet /home/iceberg/data
COPY notebooks/ /home/iceberg/notebooks

# Add notebook commands
RUN echo '#!/bin/sh' >> /bin/notebook \
    && echo 'export PYSPARK_DRIVER_PYTHON=jupyter-notebook' >> /bin/notebook \
    && echo "export PYSPARK_DRIVER_PYTHON_OPTS=\"--notebook-dir=/home/iceberg/notebooks --ip='*' --NotebookApp.token='' --NotebookApp.password='' --port=8888 --no-browser --allow-root\"" >> /bin/notebook \
    && echo "pyspark" >> /bin/notebook \
    && chmod u+x /bin/notebook

RUN echo '#!/bin/sh' >> /bin/pyspark-notebook \
    && echo 'export PYSPARK_DRIVER_PYTHON=jupyter-notebook' >> /bin/pyspark-notebook \
    && echo "export PYSPARK_DRIVER_PYTHON_OPTS=\"--notebook-dir=/home/iceberg/notebooks --ip='*' --NotebookApp.token='' --NotebookApp.password='' --port=8888 --no-browser --allow-root\"" >> /bin/pyspark-notebook \
    && echo "pyspark" >> /bin/pyspark-notebook \
    && chmod u+x /bin/pyspark-notebook

# Copy IPython startup script
RUN mkdir -p /root/.ipython/profile_default/startup
COPY ipython/startup/00-prettytables.py /root/.ipython/profile_default/startup

# Copy Spark configuration
COPY spark-defaults.conf /opt/spark/conf

# Copy PyIceberg configuration
COPY .pyiceberg.yaml /root/.pyiceberg.yaml

# Copy entrypoint script
COPY entrypoint.sh .

# Ensure entrypoint script is executable
RUN chmod +x entrypoint.sh

# Set the entrypoint
ENTRYPOINT ["/bin/bash", "./entrypoint.sh"]

# Default command
CMD ["notebook"]
