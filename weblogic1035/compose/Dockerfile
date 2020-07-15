FROM oraclelinux:7

# Environment variables required for Java and Weblogic
# ----------------------------------------------------
ENV JDK_ARCHIVE jdk-6u45-linux-x64.bin
ENV JDK_VERSION_FOLDER jdk1.6.0_45
ENV JAVA_HOME /opt/jdk1.6.0_45
ENV CONFIG_JVM_ARGS -Djava.security.egd=file:/dev/./urandom

ENV WLS_PKG wls1036_generic.jar
ENV BEA_HOME /home/user/weblogic
ENV MW_HOME /home/user/weblogic
ENV SILENT_XML silent.xml
ENV USER_MEM_ARGS -Xms256m -Xmx512m -XX:MaxPermSize=256m
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:/home/user/weblogic/wlserver/server/native/linux/x86_64

ENV MAVEN_VERSION=3.2.5
ENV M2_HOME=/home/user/apache-maven-$MAVEN_VERSION
ENV PATH=$JAVA_HOME/bin:$M2_HOME/bin:$PATH

ENV JRE_VERSION=jre-6u45-linux-x64.bin
ENV JRE_VERSION_FOLDER=jre1.6.0_45

ENV TERM xterm
ENV LANG C.UTF-8

# Needed for Codenvy workspace + Weblogic
# ---------------------------------------
EXPOSE 4403 8080 8000 22 7001

COPY ./compose/installers /home/user
WORKDIR /home/user
# Create user and install utilities for Codenvy workspace
# -------------------------------------------------------
RUN yum -y update && \
    yum -y install sudo openssh-server procps wget unzip git curl subversion nmap tar.x86_64 && \
    mkdir /var/run/sshd && \
    sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd && \
    echo "%wheel ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers && \
    useradd -u 1000 -G users,wheel -b /home -s /bin/bash -m user && \
    echo -e "codenvy2016\ncodenvy2016" | passwd user && \
    sed -i 's/requiretty/!requiretty/g' /etc/sudoers && \
    chmod +x $JRE_VERSION && \
    ./$JRE_VERSION && \
    mv $JRE_VERSION_FOLDER/ /opt && \
    echo -e "#! /bin/bash\nset -e\n" > /home/user/entrypoint.sh && \
    echo -e "sudo /usr/bin/ssh-keygen -q -N \"\" -t rsa -f /etc/ssh/ssh_host_rsa_key\nsudo /usr/bin/ssh-keygen -q -N \"\" -t dsa -f /etc/ssh/ssh_host_dsa_key\nsudo /usr/bin/ssh-keygen -q -N \"\" -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key\n" >> /home/user/entrypoint.sh && \
    echo -e "sudo /usr/sbin/sshd -D &\nexec \"\$@\"\n" >> /home/user/entrypoint.sh && \
    chmod a+x /home/user/entrypoint.sh

# Go to /home/user as user 'user' to proceed with installation
# ------------------------------------------------------------

USER user

LABEL che:server:8080:ref=tomcat8 che:server:8080:protocol=http che:server:8000:ref=tomcat8-debug che:server:8000:protocol=http

# Copy package
# -------------------------------------
COPY ./compose/$SILENT_XML /home/user/
COPY ./compose/installers/$WLS_PKG /home/user/

# Install Oracle JDK 7
# -------------------------------------
RUN sudo chmod -R 777 /home/user && \
    sudo chmod +x $JDK_ARCHIVE && \
    ./$JDK_ARCHIVE && \
    sudo mv $JDK_VERSION_FOLDER/ /opt

# Install Maven
# -------------------------------------
RUN mkdir /home/user/apache-maven-$MAVEN_VERSION && \
    wget -qO - "http://mirror.olnevhost.net/pub/apache/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz" \
    | tar -zx --strip-components=1 -C /home/user/apache-maven-$MAVEN_VERSION

# Install Weblogic
# -------------------------------------
RUN java -jar $WLS_PKG -mode=silent -silent_xml=$SILENT_XML && \
    rm $WLS_PKG $SILENT_XML
RUN ln -s /home/user/weblogic/wlserver_10.3 /home/user/weblogic/wlserver

ENV PATH $PATH:/home/user/weblogic/oracle_common/common/bin

# Set Weblogic environment
# -------------------------------------
RUN cd $MW_HOME/wlserver/server/bin && ./setWLSEnv.sh

# Cache Maven dependencies of a given project (EXAMPLE with console-java-simple)
# ------------------------------------------------------------------------------
#RUN mkdir /tmp/console-java-simple && \
#    cd /tmp/console-java-simple && \
#    svn checkout https://github.com/stour/console-java-simple/trunk . && \
#    mvn dependency:sources

# Define default command to start bash
# -------------------------------------
ENTRYPOINT ["/home/user/entrypoint.sh"]

CMD tail -f /dev/null
