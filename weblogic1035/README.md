# docker-WEBLOGIC 
@author Diego Torres

# Build New image
sudo docker-compose -f composer.yml build

# Up image  (use --detach if you want to set free the terminal)
sudo docker-compose -f composer.yml up

# Down image
sudo docker-compose -f composer.yml down

## TODO
 config Volume in weblogic home, build and create domain again

# How to create domain
##create domain https://docs.oracle.com/cd/E24329_01/web.1211/e24499/newdom.htm#WLDCW111
connect container (change the container name if is necessary)
``bash
sudo docker exec -it weblogic1035_weblogic_fitideas_1 /bin/sh
```
```bash
cd  WLHOME/common/bin
sh config.sh
```
and follow the instructions