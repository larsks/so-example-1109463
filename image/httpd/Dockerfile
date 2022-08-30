FROM registry.access.redhat.com/ubi9/httpd-24

USER root
RUN yum -y install mod_ldap
RUN rm /etc/httpd/conf.d/welcome.conf
USER default
