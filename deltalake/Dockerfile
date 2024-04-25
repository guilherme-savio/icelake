FROM python:3.10-bullseye

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
ENV DELTALAKE_VERSION="3.1.0"
ENV PATH="/opt/spark/sbin:/opt/spark/bin:${PATH}"

# Download Spark
RUN mkdir -p ${SPARK_HOME} \
    && curl https://dlcdn.apache.org/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop3.tgz -o spark-${SPARK_VERSION}-bin-hadoop3.tgz \
    && tar xvzf spark-${SPARK_VERSION}-bin-hadoop3.tgz --directory /opt/spark --strip-components 1 \
    && rm -rf spark-${SPARK_VERSION}-bin-hadoop3.tgz

# Download AWS jars to use MinIO
RUN curl https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-aws/3.3.4/hadoop-aws-3.3.4.jar -Lo /opt/spark/jars/hadoop-aws-3.3.4.jar
RUN curl https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-bundle/1.12.262/aws-java-sdk-bundle-1.12.262.jar -Lo /opt/spark/jars/aws-java-sdk-bundle-1.12.262.jar

# Install AWS CLI
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip \
    && ./aws/install \
    && rm awscliv2.zip \
    && rm -rf aws/

# Set IJava classpath
ENV IJAVA_CLASSPATH="/opt/spark/jars/*"

# Copy data and notebooks
RUN mkdir -p /home/deltalake/data /home/deltalake/notebooks /home/deltalake/spark-events
COPY /climate_change.parquet /home/deltalake/data
COPY notebooks/ /home/deltalake/notebooks

# Add notebook commands
RUN echo '#!/bin/sh' >> /bin/notebook \
    && echo 'export PYSPARK_DRIVER_PYTHON=jupyter-notebook' >> /bin/notebook \
    && echo "export PYSPARK_DRIVER_PYTHON_OPTS=\"--notebook-dir=/home/deltalake/notebooks --ip='*' --NotebookApp.token='' --NotebookApp.password='' --port=8881 --no-browser --allow-root\"" >> /bin/notebook \
    && echo "pyspark" >> /bin/notebook \
    && chmod u+x /bin/notebook

RUN echo '#!/bin/sh' >> /bin/pyspark-notebook \
    && echo 'export PYSPARK_DRIVER_PYTHON=jupyter-notebook' >> /bin/pyspark-notebook \
    && echo "export PYSPARK_DRIVER_PYTHON_OPTS=\"--notebook-dir=/home/deltalake/notebooks --ip='*' --NotebookApp.token='' --NotebookApp.password='' --port=8881 --no-browser --allow-root\"" >> /bin/pyspark-notebook \
    && echo "pyspark" >> /bin/pyspark-notebook \
    && chmod u+x /bin/pyspark-notebook


# Start the container with root privileges
CMD ["python3", "-m", "jupyter", "lab", "--ip", "0.0.0.0", "--allow-root", "--port", "8881", "--NotebookApp.token=''", "--NotebookApp.password=''", "--notebook-dir=/home/deltalake/notebooks/"]