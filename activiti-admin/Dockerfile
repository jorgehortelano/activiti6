FROM tomcat:7-jre8-alpine

ARG tomcat_folder
ARG activiti_mysql_database
ARG activiti_mysql_user
ARG activiti_mysql_password
ARG activiti_security_key

ARG activiti_admin_user
ARG activiti_admin_password

ARG machine_domain

#Set timezone.
ENV TZ=Europe/Madrid
RUN apk add --no-cache curl tzdata && \
	ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#Download activiti and install it
RUN apk --no-cache add openssl supervisor ttf-dejavu && \
	#Remove preinstalled stuff
        rm -rf ${tomcat_folder}/webapps/* && \
	mkdir -p ${tomcat_folder}/webapps/ROOT && \
	wget -q https://github.com/Activiti/Activiti/releases/download/activiti-6.0.0/activiti-6.0.0.zip && \
	apk del openssl && \
	# Install app
	unzip -qqq activiti*.zip && \
	rm activiti*zip && \
	mv activiti-*/wars/activiti-admin.war ${tomcat_folder}/webapps/ && \
	rm -r activiti-* && \
	mkdir -p ${tomcat_folder}/webapps/activiti-admin && \
	unzip -qqq -d ${tomcat_folder}/webapps/activiti-admin ${tomcat_folder}/webapps/activiti-admin.war

#Copy configuration files.
COPY lib/mysql-connector-java-5.1.26-bin.jar   ${tomcat_folder}/lib/
COPY config/server.xml   ${tomcat_folder}/conf/
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY config/activiti-admin.properties  ${tomcat_folder}/webapps/activiti-admin/WEB-INF/classes/META-INF/activiti-admin/activiti-admin.properties

#Update machine config.
RUN sed -i "s|MACHINE_DOMAIN|${machine_domain}|g" ${tomcat_folder}/conf/server.xml  && \
	sed -i "s|TOMCAT_FOLDER|${tomcat_folder}|g" /etc/supervisor/conf.d/supervisord.conf
	
#Update Activiti Admin connection.
RUN sed -i "s|ACTIVITI_MYSQL_DATABASE|${activiti_mysql_database}|g"  ${tomcat_folder}/webapps/activiti-admin/WEB-INF/classes/META-INF/activiti-admin/activiti-admin.properties && \
	sed -i "s|ACTIVITI_MYSQL_USER|${activiti_mysql_user}|g"  ${tomcat_folder}/webapps/activiti-admin/WEB-INF/classes/META-INF/activiti-admin/activiti-admin.properties && \
	sed -i "s|ACTIVITI_MYSQL_PASSWORD|${activiti_mysql_password}|g" ${tomcat_folder}/webapps/activiti-admin/WEB-INF/classes/META-INF/activiti-admin/activiti-admin.properties && \
	sed -i "s|ACTIVITI_ADMIN_USER|${activiti_admin_user}|g"  ${tomcat_folder}/webapps/activiti-admin/WEB-INF/classes/META-INF/activiti-admin/activiti-admin.properties && \
	sed -i "s|ACTIVITI_ADMIN_PASSWORD|${activiti_admin_password}|g"  ${tomcat_folder}/webapps/activiti-admin/WEB-INF/classes/META-INF/activiti-admin/activiti-admin.properties && \
	sed -i "s|SECURITY_KEY|${activiti_security_key}|g"  ${tomcat_folder}/webapps/activiti-admin/WEB-INF/classes/META-INF/activiti-admin/activiti-admin.properties
	
# Ports
EXPOSE 8080

# EXEC
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
