# Ptreodactly1.6


      INSTALL THE PANEL 

 
mkdir pterodactyl
 
cd pterodactyl
 
mkdir panel
 
cd panel
 
nano docker-compose.yml 

version: '3.8'
x-common:
  database:
    &db-environment
    # Do not remove the "&db-password" from the end of the line below, it is important
    # for Panel functionality.
    MYSQL_PASSWORD: &db-password "CHANGE_ME"
    MYSQL_ROOT_PASSWORD: "CHANGE_ME_TOO"
  panel:
    &panel-environment
    # This URL should be the URL that your reverse proxy routes to the panel server
    APP_URL: "https://pterodactyl.example.com"
    # A list of valid timezones can be found here: http://php.net/manual/en/timezones.php
    APP_TIMEZONE: "UTC"
    APP_SERVICE_AUTHOR: "noreply@example.com"
    TRUSTED_PROXIES: "*" # Set this to your proxy IP
    # Uncomment the line below and set to a non-empty value if you want to use Let's Encrypt
    # to generate an SSL certificate for the Panel.
    # LE_EMAIL: ""
  mail:
    &mail-environment
    MAIL_FROM: "noreply@example.com"
    MAIL_DRIVER: "smtp"
    MAIL_HOST: "mail"
    MAIL_PORT: "1025"
    MAIL_USERNAME: ""
    MAIL_PASSWORD: ""
    MAIL_ENCRYPTION: "true"
 
#
# ------------------------------------------------------------------------------------------
# DANGER ZONE BELOW
#
# The remainder of this file likely does not need to be changed. Please only make modifications
# below if you understand what you are doing.
#
services:
  database:
    image: mariadb:10.5
    restart: always
    command: --default-authentication-plugin=mysql_native_password
    volumes:
      - "/srv/pterodactyl/database:/var/lib/mysql"
    environment:
      <<: *db-environment
      MYSQL_DATABASE: "panel"
      MYSQL_USER: "pterodactyl"
  cache:
    image: redis:alpine
    restart: always
  panel:
    image: ghcr.io/pterodactyl/panel:latest
    restart: always
    ports:
      - "80:80"
      - "443:443"
    links:
      - database
      - cache
    volumes:
      - "/srv/pterodactyl/var/:/app/var/"
      - "/srv/pterodactyl/nginx/:/etc/nginx/http.d/"
      - "/srv/pterodactyl/certs/:/etc/letsencrypt/"
      - "/srv/pterodactyl/logs/:/app/storage/logs"
    environment:
      <<: [*panel-environment, *mail-environment]
      DB_PASSWORD: *db-password
      APP_ENV: "production"
      APP_ENVIRONMENT_ONLY: "false"
      CACHE_DRIVER: "redis"
      SESSION_DRIVER: "redis"
      QUEUE_DRIVER: "redis"
      REDIS_HOST: "cache"
      DB_HOST: "database"
      DB_PORT: "3306"
networks:
  default:
    ipam:
      config:
        - subnet: 172.20.0.0/16

docker-compose up -d
 
Create a user for the panel:
 
docker-compose run --rm panel php artisan p:user:make


                  WINGS

mkdir pterodactyl
 
cd pterodactyl

mkdir wings
 
cd wings

nano docker-compose.yml 

version: '3.8'

services:
  wings:
    image: ghcr.io/pterodactyl/wings:v1.6.1
    restart: always
    networks:
      - wings0
    ports:
      - "8080:8080"
      - "2022:2022"
      - "443:443"
    tty: true
    environment:
      TZ: "UTC"
      WINGS_UID: 988
      WINGS_GID: 988
      WINGS_USERNAME: pterodactyl
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/docker/containers/:/var/lib/docker/containers/
      - /etc/pterodactyl/:/etc/pterodactyl/
      - /var/lib/pterodactyl/:/var/lib/pterodactyl/
      - /var/log/pterodactyl/:/var/log/pterodactyl/
      - /tmp/pterodactyl/:/tmp/pterodactyl/
      - /etc/ssl/certs:/etc/ssl/certs:ro
      # Optional: If upgrading from older daemon versions
      # - /srv/daemon-data/:/srv/daemon-data/
      # Optional: Required for SSL if using Let's Encrypt
      # - /etc/letsencrypt/:/etc/letsencrypt/

networks:
  wings0:
    name: wings0
    driver: bridge
    ipam:
      config:
        - subnet: 172.21.0.0/16
    driver_opts:
      com.docker.network.bridge.name: wings0


 ssh -R 80:localhost:443 serveo.net 

 
* Create new tewrminal

cd pterodactyl

sudo su

nano /etc/pterodactyl/config.yml (PASTE UR NODE CONFIGURATION)

ls

cd wings

docker-compose up -d --force-recreate 
