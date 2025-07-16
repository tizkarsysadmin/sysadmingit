FROM ubuntu:22.04
LABEL author="zien tizkar  zein_tiz@yahoo.com"

# Preconfigure timezone and other interactive prompts
ENV DEBIAN_FRONTEND=noninteractive
RUN ln -fs /usr/share/zoneinfo/UTC /etc/localtime


# Update package list and install necessary packages
RUN apt-get update && apt-get install -y \
    lsb-release\
    ca-certificates\
    php-mbstring\
    php\
    php-snmp\
    php-gd\
    php-ldap\
    php-curl\
    php-xml\
    php-mysql\
    php-zip\
    php-intl\
    mariadb-server\
    tzdata\
    locales-all\
    graphviz\
    locales\
    vim\
    wget\
    sudo

#start service
RUN service apache2 start

#generate ssl key
RUN sudo openssl req -x509 -newkey rsa:4096 -keyout /etc/ssl/private/localhost.key -out /etc/ssl/certs/localhost.crt -sha256 -days 3650 -nodes -subj "/C=XX/ST=StateName/L=CityName/O=CompanyName/OU=CompanySectionName/CN=$(hostname)"


#copy apache conf file from host 
COPY conf/opendcim.conf /etc/apache2/sites-available/opendcim.conf

 

#Create opendcim directories and get application from github

RUN sudo mkdir -p /opt/openDCIM/\
    && cd /opt/openDCIM/\
    && sudo wget https://github.com/opendcim/openDCIM/archive/refs/tags/23.04.tar.gz\
    && sudo tar -zxpf 23.04.tar.gz\
    && sudo ln -s openDCIM-23.04 opendcim\
    && sudo mkdir -p assets/{pictures,drawings}\
    && sudo chown www-data:www-data assets/*\
    && sudo ln -s /opt/openDCIM/assets/ /opt/openDCIM/opendcim/\
    && sudo cp opendcim/db.inc.php-dist opendcim/db.inc.php


#enter db host address 172.26.145.23 into db.inc.php
RUN cd /opt/openDCIM/opendcim/\
    && sudo sed -i "/OPENDCIM_DB_HOST/s/localhost/172.26.145.23/" db.inc.php\
    && sudo htpasswd -cb /opt/openDCIM/.htpasswd dcim dcim

   
#run apache service and mods

RUN sudo a2enmod rewrite\
    && sudo a2enmod ssl\
    && sudo a2ensite opendcim.conf\
    && service apache2 restart

EXPOSE 80
EXPOSE 443


# Start services
CMD ["/bin/bash", "-c", "service apache2 start && tail -f /dev/null"]
