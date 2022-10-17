# The FROM command sets the base image that will be used in the instructions following the command.
# In our case we'll be using an OpenJDK image based on Alpine Linux. This distribution of Linux is designed to be especially lightweight and will be missing many of the tools common in most distributions.

FROM liferay/portal:7.4.3.46-ga46

ENV LIFERAY_HOME=/opt/liferay
ENV TOMCAT_HOME=$LIFERAY_HOME/tomcat

WORKDIR $LIFERAY_HOME

USER liferay:liferay

COPY --chown=liferay:liferay ./config/portal-ext.properties $LIFERAY_HOME/portal-ext.properties
# Configure Liferay to point:q to an external Elasticsearch container for production use
COPY --chown=liferay:liferay config/com.liferay.portal.search.elasticsearch7.configuration.ElasticsearchConfiguration.config $LIFERAY_HOME/osgi/configs/com.liferay.portal.search.elasticsearch7.configuration.ElasticsearchConfiguration.config

# Configure the AdvancedFileSystemStore
COPY ./config/com.liferay.portal.store.file.system.configuration.AdvancedFileSystemStoreConfiguration.config $LIFERAY_HOME/osgi/configs/com.liferay.portal.store.file.system.configuration.AdvancedFileSystemStoreConfiguration.config

# Configure cluster link to use JDBC PING so that the cluster network works in the cloud
COPY --chown=liferay:liferay ./config/jdbc_ping.xml $TOMCAT_HOcdME/webapps/ROOT/WEB-INF/classes/jdbc_ping.xml

COPY --chown=liferay:liferay ./config/wait-for-it.sh /etc/liferay/wait-for-it.sh
RUN chmod +x /etc/liferay/wait-for-it.sh

CMD /etc/liferay/wait-for-it.sh -s -t 60 mysql8:3306 -- $TOMCAT_HOME/bin/catalina.sh run
