FROM confluentinc/cp-kafka-connect:7.3.5

ARG JDBC_PLUGIN_PATH
ARG DEBEZIUM_CONNECTOR_POSTGRES=${DEBEZIUM_CONNECTOR_POSTGRES}
ARG POSTGRES_VERSION=42.5.1
ARG KAFKA_CONNECT_JDBC_VERSION
ARG JDBC_MYSQL_CONNECTOR

WORKDIR /usr/share/java

RUN mkdir -p ${JDBC_PLUGIN_PATH}

# Install Debezium PostgreSQL connector
RUN curl -sO https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/${DEBEZIUM_CONNECTOR_POSTGRES}.Final/debezium-connector-postgres-${DEBEZIUM_CONNECTOR_POSTGRES}.Final-plugin.tar.gz && \
    tar xfz debezium-connector-postgres-${DEBEZIUM_CONNECTOR_POSTGRES}.Final-plugin.tar.gz -C $PWD && \
    mv debezium-connector-postgres/*.jar ./

# Install PostgreSQL JDBC Driver
#RUN curl -sO https://jdbc.postgresql.org/download/postgresql-${POSTGRES_VERSION}.jar && \
#    mv postgresql-${POSTGRES_VERSION}.jar ${JDBC_PLUGIN_PATH}

# Install JDBC for Kafka Connect
RUN curl -sO https://packages.confluent.io/maven/io/confluent/kafka-connect-jdbc/${KAFKA_CONNECT_JDBC_VERSION}/kafka-connect-jdbc-${KAFKA_CONNECT_JDBC_VERSION}.jar && \
    mv kafka-connect-jdbc-${KAFKA_CONNECT_JDBC_VERSION}.jar ${JDBC_PLUGIN_PATH}

# Install MySQL JDBC Driver
RUN curl -sO https://repo1.maven.org/maven2/mysql/mysql-connector-java/${JDBC_MYSQL_CONNECTOR}/mysql-connector-java-${JDBC_MYSQL_CONNECTOR}.jar && \
    mv mysql-connector-java-${JDBC_MYSQL_CONNECTOR}.jar ${JDBC_PLUGIN_PATH}
