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

FROM centos:centos7.6.1810

# Enable Systemd Integration (https://hub.docker.com/_/centos)
ENV container=docker

RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done) \
	&& rm -f /lib/systemd/system/multi-user.target.wants/* \
	&& rm -f /etc/systemd/system/*.wants/* \
	&& rm -f /lib/systemd/system/local-fs.target.wants/* \
	&& rm -f /lib/systemd/system/sockets.target.wants/*udev* \
	&& rm -f /lib/systemd/system/sockets.target.wants/*initctl* \
	&& rm -f /lib/systemd/system/basic.target.wants/* \
	&& rm -f /lib/systemd/system/anaconda.target.wants/*

# Install GNOME Desktop Environment (https://github.com/nutsllc/vnc-centos-gnome/blob/master/centos6-gnome/Dockerfile)
RUN yum update -y \
	&& yum groupinstall -y "GNOME Desktop" \
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

# Install Tiger VNC (https://www.tecmint.com/install-and-configure-vnc-server-in-centos-7)
RUN yum update -y \
	&& yum install -y tigervnc-server \
	&& yum clean all \
	&& rm -rf /tmp/*

# Install Tiger VNC as a Service
RUN { \
		echo '[Unit]'; \
		echo 'Description=Remote desktop service (VNC)'; \
		echo 'After=syslog.target network.target'; \
		\
		echo '[Service]'; \
		echo 'Type=forking'; \
		echo 'ExecStartPre=/bin/sh -c "/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :"'; \
		echo 'ExecStart=/usr/sbin/runuser -l root -c "/usr/bin/vncserver :0 -depth 24 -geometry 1280x960 %i"'; \
		echo 'PIDFile=/root/.vnc/%H%i.pid'; \
		echo 'ExecStop=/bin/sh -c "/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :"'; \
		\
		echo '[Install]'; \
		echo 'WantedBy=multi-user.target'; \
	} > /etc/systemd/system/vncserver@:0.service \
	\
	&& chmod -v +x /etc/systemd/system/vncserver@:0.service \
	&& chkconfig vncserver@:0 on 3456

# Set the Root Password
ENV ROOT_PASSWORD=password
RUN su root sh -c "yes $ROOT_PASSWORD | vncpasswd" \
	&& echo "root:$ROOT_PASSWORD" | chpasswd

# Install Git
RUN yum update -y \
	&& yum install -y git \
	&& yum clean all \
	&& rm -rf /tmp/*

# Clone Liferay Portal
RUN mkdir -p /opt/dev/projects/github \
	&& cd /opt/dev/projects/github \
	&& git clone https://github.com/liferay/liferay-portal.git

# Setup Firefox Settings
RUN yum update -y \
	&& yum install -y firefox \
	&& rm -rf /opt/firefox \
	&& yum clean all \
	&& rm -rf /tmp/*

# Install Firefox 60.4.0esr
ARG FIREFOX_VERSION=60.4.0esr
RUN FIREFOX_DOWNLOAD_URL="https://download-installer.cdn.mozilla.net/pub/firefox/releases/$FIREFOX_VERSION/linux-x86_64/en-US/firefox-$FIREFOX_VERSION.tar.bz2" \
	&& wget --no-verbose -O /tmp/firefox.tar.bz2 $FIREFOX_DOWNLOAD_URL \
	&& tar -C /opt -xjf /tmp/firefox.tar.bz2 \
	&& rm -f /tmp/firefox.tar.bz2 \
	&& mv /opt/firefox /opt/firefox-$FIREFOX_VERSION \
	&& echo 'user_pref("app.update.enabled", false);' >> /opt/firefox-$FIREFOX_VERSION/defaults/pref/channel-prefs.js

# Install Firefox 52.0.2esr
ARG FIREFOX_VERSION=52.0.2esr
RUN FIREFOX_DOWNLOAD_URL="https://download-installer.cdn.mozilla.net/pub/firefox/releases/$FIREFOX_VERSION/linux-x86_64/en-US/firefox-$FIREFOX_VERSION.tar.bz2" \
	&& wget --no-verbose -O /tmp/firefox.tar.bz2 $FIREFOX_DOWNLOAD_URL \
	&& tar -C /opt -xjf /tmp/firefox.tar.bz2 \
	&& rm -f /tmp/firefox.tar.bz2 \
	&& mv /opt/firefox /opt/firefox-$FIREFOX_VERSION \
	&& echo 'user_pref("app.update.enabled", false);' >> /opt/firefox-$FIREFOX_VERSION/defaults/pref/channel-prefs.js

# Configure Default Firefox to FF52
ARG FIREFOX_VERSION=52.0.2esr
RUN ln -fs /opt/firefox-$FIREFOX_VERSION/firefox /usr/bin/firefox \
	&& ln -fs /opt/firefox-$FIREFOX_VERSION /opt/firefox

# Install Oracle JDK 8
ARG JAVA_DOWNLOAD_URL=https://download.oracle.com/otn-pub/java/jdk/8u201-b09/42970487e3af4f5aa5bca3f542482c60/jdk-8u201-linux-x64.tar.gz
RUN mkdir -p /opt/java/ \
	&& wget --no-cookies --no-check-certificate --header "Cookie: oraclelicense=accept-securebackup-cookie" "$JAVA_DOWNLOAD_URL" -P /opt/java/ \
	&& tar -xf /opt/java/jdk-8u201-linux-x64.tar.gz -C /opt/java/ \
	&& ln -fs /opt/java/jdk1.8.0_201 /opt/java/jdk8 \
	&& rm /opt/java/jdk-8u201-linux-x64.tar.gz

# Install Open JDK 11
ARG JAVA_DOWNLOAD_URL=https://download.java.net/openjdk/jdk11/ri/openjdk-11+28_linux-x64_bin.tar.gz
RUN mkdir -p /opt/java/ \
	&& wget $JAVA_DOWNLOAD_URL -P /opt/java/ \
	&& tar -xf /opt/java/openjdk-11+28_linux-x64_bin.tar.gz -C /opt/java/ \
	&& ln -fs /opt/java/jdk-11 /opt/java/jdk11 \
	&& rm /opt/java/openjdk-11+28_linux-x64_bin.tar.gz

# Configure Default JDK to JDK8
RUN ln -fs /opt/java/jdk8 /opt/java/jdk

# Install Apache Ant 1.9.4
RUN mkdir -p /opt/java/ant/ \
	&& wget http://archive.apache.org/dist/ant/binaries/apache-ant-1.9.4-bin.tar.gz -P /opt/java/ant \
	&& tar -xf /opt/java/ant/apache-ant-1.9.4-bin.tar.gz -C /opt/java/ant/ \
	&& mv /opt/java/ant/apache-ant-*/* /opt/java/ant \
	&& rm -fr /opt/java/ant/apache-ant-*

# Install Database Clients
RUN yum install -y https://download.postgresql.org/pub/repos/yum/11/redhat/rhel-7-x86_64/pgdg-centos11-11-2.noarch.rpm \
	&& sudo yum install -y \
		mysql \
		postgresql11 \
	&& yum clean all \
	&& rm -rf /tmp/*

# Set Environment Variables
ENV ANT_HOME=/opt/java/ant
ENV ANT_OPTS="-Xms1024m -Xmx3072m"
ENV JAVA_HOME="/opt/java/jdk"
ENV JAVA_OPTS="-Xms1024m -Xmx3072m"
ENV DISPLAY=":0"

RUN echo 'PATH="/opt/java/ant/bin:/opt/java/jdk/bin:${PATH}"' > /root/.bash_profile

ENV PATH="/opt/java/ant/bin:/opt/java/jdk/bin:${PATH}"

# Start a Login Session (https://hub.docker.com/_/centos)
ENTRYPOINT /usr/sbin/init