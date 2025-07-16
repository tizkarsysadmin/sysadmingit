
from 2.3 to 2.6 use below script and new docker volume

docker cp itop-dump.sql itop-db:/itop-dump.sql
docker exec -it itop-db bash
mysql -u itopuser -p itop < /dump.sql
# enter password (itoppass)
mysql -u root -p itop < /dump.sql


docker cp itop-dump.sql itop-db:/itop-dump.sql
docker exec -it itop-db bash

 mysql -u root -p itop < /itop-dump.sql
zizi

from 2.6 to 2.7 
install this extension and mount external volume and config file

download combodo-views-1.0.0-653.zip from this extension and unzip it

https://github.com/Combodo/combodo-views/releases/tag/1.0.0

cp into container in directory 

https://www.itophub.io/wiki/page?id=extensions:installation

copy its folder in extension folder in itop container you can use docker cp command


in 2.7 2.6 volume db and ap plus directory extension:



2.7 after 2.6 volume db and ap plus:
docker cp config-itop.php itop:/var/www/html/
docker exec -it itop bash
chmod 644 /var/www/html/config-itop.php
chown www-data:www-data /var/www/html/config-itop.php


from 2.7 till ;atest 3.2 just itop image replace in docker compose file and copy config file and change permission:

in docker compose :
    
MARIADB_AUTO_UPGRADE: 1



docker cp config-itop.php itop:/var/www/html/
docker exec -it itop bash
chmod 644 /var/www/html/config-itop.php
chown www-data:www-data /var/www/html/config-itop.php


end



hit: 
to check if volume mounted correctly use this command:

docker run --rm -v v260_itop_data26:/data alpine ls -l /data








docker cp /home/user01/iTop-icon.png itop:/var/www/html/images/
Successfully copied 95.2kB to itop:/var/www/html/images/
root@itc-srvint:~#
root@itc-srvint:~#  docker cp /home/user01/itop-logo-external.png itop:/var/www/html/images/
Successfully copied 95.2kB to itop:/var/www/html/images/
root@itc-srvint:~#
root@itc-srvint:~# docker cp /home/user01/itop-logo.png itop:/var/www/html/images/
Successfully copied 95.2kB to itop:/var/www/html/images/
root@itc-srvint:~#
root@itc-srvint:~# docker cp /home/user01/iTop-icon.ico itop:/var/www/html/images/
Successfully copied 6.14kB to itop:/var/www/html/images/
root@itc-srvint:~#
root@itc-srvint:~#  docker cp /home/user01/favicon.ico itop:/var/www/html/images/
Successfully copied 6.14kB to itop:/var/www/html/images/
root@itc-srvint:~#
root@itc-srvint:~#





 refrence:


 https://www.itophub.io/wiki/page?id=latest:install:update_wizard
 https://www.itophub.io/wiki/page?id=2_7_0:install:upgrading_itop
 https://github.com/vbkunin/itop-docker
 https://hub.docker.com/r/vbkunin/itop
 https://hub.docker.com/_/mariadb/tags?name=lts

https://www.itophub.io/wiki/page?id=extensions:combodo-views
https://github.com/Combodo/combodo-views/releases/tag/1.0.0

