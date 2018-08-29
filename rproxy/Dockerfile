FROM nginx:stable-alpine

ARG machine_domain
ARG formrunner_virtual_path

ENV HTML_ROOT_FOLDER /www

#Set timezone.
ENV TZ=Europe/Madrid
RUN apk add --no-cache curl tzdata && \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN mkdir -p /${HTML_ROOT_FOLDER}

EXPOSE 443

COPY config/servers.conf  /etc/nginx/conf.d/default.conf

#Update Domain
RUN sed -i "s|MACHINE_DOMAIN|${machine_domain}|g" /etc/nginx/conf.d/default.conf && \
	formrunner_virtual_path_without_spaces="$(echo -e "${formrunner_virtual_path}" | sed -e 's/^[[:space:]]*//')" && \
	sed -i "s|FORM_RUNNER_VIRTUAL_PATH|${formrunner_virtual_path_without_spaces}|g" /etc/nginx/conf.d/default.conf && \
	sed -i "s|ROOT_FOLDER|${HTML_ROOT_FOLDER}|g" /etc/nginx/conf.d/default.conf

CMD ["nginx", "-g", "daemon off;"]
