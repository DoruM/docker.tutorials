FROM kaazing/enterprise-gateway:latest

# Generate Keystore entry for kaazing.example.com

RUN keytool -genkeypair -keystore conf/keystore.db -storetype JCEKS -keypass ab987c -storepass ab987c -alias kaazing.example.com -keyalg RSA -dname "CN=kaazing.example.com, OU=Example, O=Example, L=Mountain View, ST=California, C=US"

RUN keytool -exportcert -alias kaazing.example.com -keystore conf/keystore.db -storetype JCEKS -file conf/hostname.cer -storepass ab987c
RUN keytool -importcert -keystore conf/truststore.db -storepass changeit -storetype JCEKS -alias kaazing.example.com -file conf/hostname.cer -noprompt

# Add and Build Javascript Client tutorials to web directory so we can test it from a browser,
# TODO pull in built tutorials when they are released

RUN set -ex \
  && for key in \
    9554F04D7259F04124DE6B476D5A82AC7E37093B \
    94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
    0034A06D9D9B0064CE8ADF6BF1747F4AD2306D93 \
    FD3A5288F042B6850C66B31F09FE44734EB7990E \
    71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
    DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
    B9AE9905FFD7803F25714661B63B535A4C206CA9 \
    C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
  ; do \
    gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
  done

ENV NODE_VERSION 0.10.45

RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
  && gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
  && grep " node-v$NODE_VERSION-linux-x64.tar.xz\$" SHASUMS256.txt | sha256sum -c - \
  && tar -xJf "node-v$NODE_VERSION-linux-x64.tar.xz" -C /usr/local --strip-components=1 \
  && rm "node-v$NODE_VERSION-linux-x64.tar.xz" SHASUMS256.txt.asc SHASUMS256.txt

RUN git clone https://github.com/kaazing/javascript.client.tutorials ./web/javascript.client.tutorials
RUN npm --prefix ./web/javascript.client.tutorials/amqp install ./web/javascript.client.tutorials/amqp 

# Add rabbitmq client libs
ADD https://github.com/rabbitmq/rabbitmq-server/releases/download/rabbitmq_v3_6_2/rabbitmq-java-client-bin-3.6.2.tar.gz lib/ext/

# Add gateway config
COPY amqp-internal-gateway-config.xml conf/gateway-config.xml
COPY log4j-config.xml conf/log4j-config.xml

