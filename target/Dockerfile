FROM payara/server-full:4.181
MAINTAINER Han-Kie Dang han-kie.dang@nttdata.com

ARG domainname=rsu-adapter

ENV ADMIN_USER admin
ENV ADMIN_PASSWORD admin
ENV GLASSFISH_HOME /opt/payara41
ENV BASE_PORT=8000

ENV CREATE_SH create_domain.sh
ENV INFO_SH info.sh

USER root

# create admin password file
RUN echo 'AS_ADMIN_PASSWORD=' > /tmp-passwords
RUN echo 'AS_ADMIN_NEWPASSWORD=admin' >> /tmp-passwords
RUN echo 'AS_ADMIN_USERPASSWORD=' >> /tmp-passwords
RUN echo 'AS_ADMIN_USERPASSWORD=password' > /tmp-passwords2

# put domain script and customization info in container
COPY data/$CREATE_SH $GLASSFISH_HOME/bin/$CREATE_SH.tmp
# removing Windows line endings
RUN tr -d '\r' < $GLASSFISH_HOME/bin/$CREATE_SH.tmp > $GLASSFISH_HOME/bin/$CREATE_SH

RUN chmod +x $GLASSFISH_HOME/bin/$CREATE_SH
COPY data/payara-customization $GLASSFISH_HOME/bin/payara-customization

# actually run domain creation
RUN echo "$domainname $BASE_PORT"
RUN sh $GLASSFISH_HOME/bin/create_domain.sh $domainname $BASE_PORT

# let's have some info:
COPY data/${INFO_SH} ${GLASSFISH_HOME}/bin/${INFO_SH}.tmp
# removing Windows line endings
RUN tr -d '\r' < ${GLASSFISH_HOME}/bin/${INFO_SH}.tmp > ${GLASSFISH_HOME}/bin/${INFO_SH}
RUN chmod +x ${GLASSFISH_HOME}/bin/${INFO_SH}
RUN ${GLASSFISH_HOME}/bin/${INFO_SH}

# expose Payara ports (Java Debug, Admin, OSGi, HTTP, HTTPS)
EXPOSE 8009 8048 8066 8080 8081

# provide some default values to start domain each time
ENTRYPOINT ["/opt/payara41/bin/asadmin"]
CMD ["start-domain", "--verbose","--debug","true", "rsu-adapter"]