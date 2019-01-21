# The MIT License
#
#  Copyright (c) 2015-2017, CloudBees, Inc. and other Jenkins contributors
#
#  Permission is hereby granted, free of charge, to any person obtaining a copy
#  of this software and associated documentation files (the "Software"), to deal
#  in the Software without restriction, including without limitation the rights
#  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
#  copies of the Software, and to permit persons to whom the Software is
#  furnished to do so, subject to the following conditions:
#
#  The above copyright notice and this permission notice shall be included in
#  all copies or substantial portions of the Software.
#
#  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
#  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
#  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
#  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
#  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
#  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
#  THE SOFTWARE.

FROM centos:centos6.10

MAINTAINER Michael Hashimoto <michael.hashimoto@liferay.com>

# Install GNOME Desktop Environment (https://github.com/nutsllc/vnc-centos-gnome/blob/master/centos6-gnome/Dockerfile)
RUN yum update -y \
    && yum groupinstall -y "Desktop" \
    && yum clean all \
    && rm -rf /tmp/*

# Configure GNOME Desktop Environment
RUN gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type bool --set /apps/nautilus/preferences/always_use_browser true \
    && gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type bool --set /apps/gnome-screensaver/idle_activation_enabled false \
    && gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type bool --set /apps/gnome-screensaver/lock_enabled false \
    && gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type int --set /apps/metacity/general/num_workspaces 1 \
    && gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type=string --set /apps/gnome_settings_daemon/keybindings/screensaver ' ' \
    && gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type=string --set /apps/gnome_settings_daemon/keybindings/power ' ' \
    && gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type bool --set /apps/panel/global/disable_log_out true \
    && gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type int --set /apps/gnome-power-manager/timeout/sleep_computer_ac '0' \
    && gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type int --set /apps/gnome-power-manager/timeout/sleep_display_ac '0' \
    && gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type int --set /apps/gnome-screensaver/power_management_delay '0' \
    && gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type bool --set /desktop/gnome/remote_access/enabled true \
    && gconftool-2 --direct --config-source xml:readwrite:/etc/gconf/gconf.xml.mandatory --type bool --set /desktop/gnome/remote_access/prompt_enabled false

# Install Supervisor (https://www.howtoforge.com/vnc-server-installation-centos-6.5)
RUN yum update -y \
    && yum install -y python-setuptools \
    && yum clean all \
    && rm -rf /tmp/* \
    \
    && easy_install supervisor \
    && mkdir -p /var/log/supervisor \
    && mkdir -p /etc/supervisord.d \
    && { \
        echo '[supervisord]'; \
        echo 'logfile=/var/log/supervisor/supervisord.log'; \
        echo 'logfile_backups=1'; \
        echo 'logfile_maxbytes=1MB'; \
        echo 'loglevel=warn'; \
        echo 'nodaemon=true'; \
        echo 'pidfile=/var/run/supervisord.pid'; \
        echo 'user=root'; \
    } > /etc/supervisord.conf

# Install Tiger VNC (https://www.howtoforge.com/vnc-server-installation-centos-6.5)
RUN yum update -y \
    && yum install -y tigervnc-server \
    && yum clean all \
    && rm -rf /tmp/*

# Configure Tiger VNC
RUN { \
        echo 'VNCSERVERS="0:root"'; \
        echo 'VNCSERVERARGS[0]="-geometry 1280x960"'; \
    } >> /etc/sysconfig/vncservers

# Add Tiger VNC to the Superviser
RUN { \
        echo '[program:vncserver]'; \
        echo 'command=/etc/init.d/vncserver restart'; \
        echo 'stderr_logfile=/var/log/supervisor/vncserver-error.log'; \
        echo 'stdout_logfile=/var/log/supervisor/vncserver.log'; \
        echo 'user=root'; \
    } >> /etc/supervisord.conf

# Set the Root Password
ENV ROOT_PASSWORD=password
RUN su root sh -c "yes $ROOT_PASSWORD | vncpasswd" \
    && echo "root:$ROOT_PASSWORD" | chpasswd

# # Install Core Utilities
# RUN yum install -y \
#         git \
#         bzip2 \
#         wget \
#     && yum clean all \
#     && rm -rf /tmp/*

# # Install Oracle JDK 8
# ARG JAVA_DOWNLOAD_URL=https://download.oracle.com/otn-pub/java/jdk/8u201-b09/42970487e3af4f5aa5bca3f542482c60/jdk-8u201-linux-x64.tar.gz
# RUN mkdir -p /opt/java/ \
#     && wget --no-cookies --no-check-certificate --header "Cookie: oraclelicense=accept-securebackup-cookie" "$JAVA_DOWNLOAD_URL" -P /opt/java/ \
#     && tar -xf /opt/java/jdk-8u201-linux-x64.tar.gz -C /opt/java/ \
#     && ln -fs /opt/java/jdk1.8.0_201 /opt/java/jdk8 \
#     && rm /opt/java/jdk-8u201-linux-x64.tar.gz

# # Install Open JDK 11
# ARG JAVA_DOWNLOAD_URL=https://download.java.net/openjdk/jdk11/ri/openjdk-11+28_linux-x64_bin.tar.gz
# RUN mkdir -p /opt/java/ \
#     && wget $JAVA_DOWNLOAD_URL -P /opt/java/ \
#     && tar -xf /opt/java/openjdk-11+28_linux-x64_bin.tar.gz -C /opt/java/ \
#     && ln -fs /opt/java/jdk-11 /opt/java/jdk11 \
#     && rm /opt/java/openjdk-11+28_linux-x64_bin.tar.gz

# # Configure Default JDK to JDK8
# RUN ln -fs /opt/java/jdk8 /opt/java/jdk

# # Install Apache Ant 1.9.4
# RUN mkdir -p /opt/java/ant/ \
#     && wget http://archive.apache.org/dist/ant/binaries/apache-ant-1.9.4-bin.tar.gz -P /opt/java/ant \
#     && tar -xf /opt/java/ant/apache-ant-1.9.4-bin.tar.gz -C /opt/java/ant/ \
#     && mv /opt/java/ant/apache-ant-*/* /opt/java/ant \
#     && rm -fr /opt/java/ant/apache-ant-*

# # Install Firefox 52.0.2esr
# ARG FIREFOX_VERSION=52.0.2esr
# RUN FIREFOX_DOWNLOAD_URL="https://download-installer.cdn.mozilla.net/pub/firefox/releases/$FIREFOX_VERSION/linux-x86_64/en-US/firefox-$FIREFOX_VERSION.tar.bz2" \
#     && yum install -y firefox \
#     && wget --no-verbose -O /tmp/firefox.tar.bz2 $FIREFOX_DOWNLOAD_URL \
#     && yum remove -y firefox \
#     && yum install -y firefox \
#     && rm -rf /opt/firefox \
#     && tar -C /opt -xjf /tmp/firefox.tar.bz2 \
#     && rm -f /tmp/firefox.tar.bz2 \
#     && mv /opt/firefox /opt/firefox-$FIREFOX_VERSION \
#     && ln -fs /opt/firefox-$FIREFOX_VERSION/firefox /usr/bin/firefox \
#     && ln -fs /opt/firefox-$FIREFOX_VERSION /opt/firefox \
#     && yum clean all

# # Set Environment Variables
# ENV ANT_HOME=/opt/java/ant
# ENV ANT_OPTS="-Xms1024m -Xmx3072m"
# ENV JAVA_HOME="/opt/java/jdk"
# ENV JAVA_OPTS="-Xms1024m -Xmx3072m"

# ENV PATH="${JAVA_HOME}/bin:${ANT_HOME}/bin:${PATH}"

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]