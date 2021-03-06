# #############################################
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# #############################################

FROM ubuntu:16.04
ENV OM_VER_MAJ='5'
ENV OM_VER_MIN='0'
ENV OM_VER_MIC='0'
ENV OM_VERSION="${OM_VER_MAJ}.${OM_VER_MIN}.${OM_VER_MIC}"
LABEL vendor="Apache OpenMeetings dev team"
LABEL version="${OM_VERSION}"
LABEL maintainer=dev@openmeetings.apache.org

ARG BUILD_TYPE="min"
ENV OM_TYPE=${BUILD_TYPE}
ENV DB_ROOT_PASS '12345'
ENV OM_USER="om_admin"
ENV OM_PASS="1Q2w3e4r5t^y"
ENV DAEMON_USER="nobody"
ENV DAEMON_UID="65534"
ENV OM_DB_NAME="openmeetings"
ENV OM_DB_TYPE="mysql"
ENV OM_DB_HOST="localhost"
ENV OM_DB_PORT="3306"
ENV OM_DB_USER="om_admin"
ENV OM_DB_PASS="12345"
ENV OM_KURENTO_WS_URL="ws://127.0.0.1:8888/kurento"
ENV OM_DATA_DIR="/opt/omdata"
ENV work=/opt
ENV OM_HOME=/opt/openmeetings
ENV MYSQL_J_VER="8.0.15"
ENV DB2_J_VER="11.1.4.4"
ENV PORTS=5443

ENV FFMPEG ${work}/ffmpeg

WORKDIR ${work}
COPY 16.04/scripts/ffmpg.sh ./

WORKDIR ${OM_HOME}
RUN cat /etc/issue \
  \
  && echo "OM server of type ${OM_TYPE} will be built" \
  \
  && apt-get update && apt-get install -y --no-install-recommends \
    apt-utils \
  && apt-get install -y --no-install-recommends \
    software-properties-common \
    unzip \
    wget \
    ghostscript \
    libgs-dev \
    imagemagick \
    sox \
    sudo \
    libreoffice \
    openjdk-8-jdk \
  && chmod a+x ${work}/ffmpg.sh \
    && ${work}/ffmpg.sh \
    && rm -rf ${FFMPEG} \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* \
  \
  && wget https://builds.apache.org/view/M-R/view/OpenMeetings/job/openmeetings/lastSuccessfulBuild/artifact/openmeetings-server/target/apache-openmeetings-5.0.0-SNAPSHOT.tar.gz -O apache-openmeetings-${OM_VERSION}.tar.gz \
    && tar -xzf apache-openmeetings-${OM_VERSION}.tar.gz \
    && rm apache-openmeetings-${OM_VERSION}.tar.gz \
    && wget https://repo1.maven.org/maven2/mysql/mysql-connector-java/${MYSQL_J_VER}/mysql-connector-java-${MYSQL_J_VER}.jar -P webapps/openmeetings/WEB-INF/lib \
    && wget https://repo1.maven.org/maven2/com/ibm/db2/jcc/${DB2_J_VER}/jcc-${DB2_J_VER}.jar -P webapps/openmeetings/WEB-INF/lib \
    && sed -i 's|<policy domain="coder" rights="none" pattern="PS" />|<!--policy domain="coder" rights="none" pattern="PS" />|g; s|<policy domain="coder" rights="none" pattern="XPS" />|<policy domain="coder" rights="none" pattern="XPS" /-->|g' /etc/ImageMagick-6/policy.xml 

WORKDIR ${work}
COPY scripts/*.sh ./

RUN chmod a+x ${work}/*.sh \
  && ./om_install.sh

EXPOSE ${PORTS}

ENTRYPOINT [ "bash", "-c", "${work}/om.sh" ]

