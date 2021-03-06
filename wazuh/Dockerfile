FROM milcom/centos7-systemd

COPY config/*.repo /etc/yum.repos.d/

RUN rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch

RUN yum -y update; yum clean all;
RUN yum -y install epel-release openssl useradd; yum clean all
RUN groupadd -g 1000 ossec
RUN useradd -u 1000 -g 1000 ossec
RUN yum install -y --nogpgcheck wazuh-manager wazuh-api


ADD config/default_agent /var/ossec/default_agent

RUN service wazuh-manager restart &&\
  /var/ossec/bin/manage_agents -f /default_agent &&\
  rm /var/ossec/default_agent &&\
  service wazuh-manager stop &&\
  echo -n "" /var/ossec/logs/ossec.log


ADD config/data_dirs.env /data_dirs.env
ADD config/init.bash /init.bash
# Sync calls are due to https://github.com/docker/docker/issues/9547
RUN chmod 755 /init.bash &&\
  sync && /init.bash &&\
  sync && rm /init.bash


ADD config/run.sh /tmp/run.sh
RUN chmod 755 /tmp/run.sh

VOLUME ["/var/ossec/data"]

EXPOSE 55000/tcp 1514/udp 1515/tcp 514/udp

# Run supervisord so that the container will stay alive

ENTRYPOINT ["/tmp/run.sh"]