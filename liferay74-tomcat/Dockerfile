# The FROM command sets the base image that will be used in the instructions following the command.
# In our case we'll be using an OpenJDK image based on Alpine Linux. This distribution of Linux is designed to be especially lightweight and will be missing many of the tools common in most distributions.

FROM liferay/portal:7.4.3.46-ga46

ENV LIFERAY_HOME=/opt/liferay
#ENV DOWNLOADS=/etc/liferay/downloads

#ENV BUNDLE_BASE=liferay-ce-portal-7.2.1-ga2
#ENV BUNDLE_FILE=liferay-ce-portal-tomcat-7.2.1-ga2-20191111141448326.zip

ENV TOMCAT_HOME=$LIFERAY_HOME/tomcat

# Here we are setting up our system. First we update Alpine's apk package manager and install packages we'll need. We'll then create a new user and group (liferay for both). Next we create the home liferay directory /opt/liferay by referencing the environment variable and finally we set the owner of the directory and files within to the new liferay user.
WORKDIR $LIFERAY_HOME

USER liferay:liferay

# Here we set the working directory to /opt/liferay by referencing the environment variable $LIFERAY_HOME. The RUN, COPY and CMD commands that follow will be executed in this working directory. We also set User and User group that will be used when running the RUN, COPY and CMD commands in the dockerfile. It's always a good idea to avoid running as the root user when possible.
#RUN mkdir -p $DOWNLOADS

#COPY ./$BUNDLE_FILE $DOWNLOADS

#RUN unzip -q $DOWNLOADS/$BUNDLE_FILE -d $DOWNLOADS && \
#	mv $DOWNLOADS/$BUNDLE_BASE/* . && \
#	rm -rf $DOWNLOADS

COPY --chown=liferay:liferay ./config/portal-ext.properties $LIFERAY_HOME/portal-ext.properties
#COPY --chown=liferay:liferay ./config/activation-key-digitalenterprisedevelopment-7.2-guillaumelenoir.xml $LIFERAY_HOME/deploy/activation-key-digitalenterprisedevelopment-7.2-guillaumelenoir.xml

# Configure Liferay to point:q to an external Elasticsearch container for production use
COPY --chown=liferay:liferay config/com.liferay.portal.search.elasticsearch7.configuration.ElasticsearchConfiguration.config $LIFERAY_HOME/osgi/configs/com.liferay.portal.search.elasticsearch7.configuration.ElasticsearchConfiguration.config

# Configure the AdvancedFileSystemStore
COPY ./config/com.liferay.portal.store.file.system.configuration.AdvancedFileSystemStoreConfiguration.config $LIFERAY_HOME/osgi/configs/com.liferay.portal.store.file.system.configuration.AdvancedFileSystemStoreConfiguration.config

# Configure cluster link to use JDBC PING so that the cluster network works in the cloud
COPY --chown=liferay:liferay ./config/jdbc_ping.xml $TOMCAT_HOcdME/webapps/ROOT/WEB-INF/classes/jdbc_ping.xml

#COPY --chown=liferay:liferay ./lib/mysql.jar $TOMCAT_HOME/lib/ext/mysql.jar

# Configure the TunnelAuthVerifier for live remote staging
#COPY ./config/com.liferay.portal.security.auth.verifier.internal.tunnel.module.configuration.TunnelAuthVerifierConfiguration-default.cfg $LIFERAY_HOME/osgi/configs/#com.liferay.portal.security.auth.verifier.internal.tunnel.module.configuration.TunnelAuthVerifierConfiguration-default.cfg

COPY --chown=liferay:liferay ./config/wait-for-it.sh /etc/liferay/wait-for-it.sh
RUN chmod +x /etc/liferay/wait-for-it.sh

CMD /etc/liferay/wait-for-it.sh -s -t 60 mysql8:3306 -- $TOMCAT_HOME/bin/catalina.sh run



# In these final lines we set up and run our tomcat bundle. We start off by creating the /etc/liferay/downloads directory.

# We then copy the bundle file provided in the the local exercise files into the container to the newly created downloads directory.

# Next we unzip the bundle, move it into the working directory and clean up the downloads folder.

# We copy over a couple of configuration files from our local machine into the container and simultaneously change the owner of those files to the liferay user.

# Note: You'll notice a couple of these configuration files are commented out. We'll take at look again at this later in the course.
# We also copy over a simple shell script, wait-for-it.sh. The script tests if a specific TCP host/port is available. It will ping the MySQL container and wait for it to start up before issuing the command in the tomcat container to start up the tomcat server . When working with containers you will run into scenarios where you'll have to manage these kinds of container dependencies.

# After copying the script over we make it executable by using the chmod +x command. (Remember, in the docker-compose file the tomcat service is dependent on a MySQL service also defined in that file as well as its respective dockerfile).

# Finally we use the CMD to provide the default command that the container will run once it has fully started up. In this case the command will run the wait-for-it script which will run the tomcat sever startup script once the MySQL container is running.

# Keep in mind that you can only have one CMD command per dockerfile. You can have multiple RUN commands which are executed when generating the image. The CMDcommand will be executed only once a container generated from an image is up and running.

# We've taken a closer look at the docker-compose and dockerfile for the tomcat server. Let's take a closer look at the MySQL service.
