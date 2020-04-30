# docker-traefik2-host

Generalized Docker server config as documented in [My dockerized-server Config](https://dlad.summittdweller.com/en/posts/042-my-dockerized-server-config/) but modified to use Traefik v2.2.

At last check, 27-Apr-2020, the previous version of this project was used on:

  - https://static.grinnell.edu  : Grinnell College hosted server
  - https://summittdweller.com   : Personal DigitalOcean VPS (`digital-ocean-containrrr` branch)

At last check, on 29-Apr-2020, this project was soon to be used on:

  - https://static.grinnell.edu : Grinnell College Libraries server for misc. "static" applications
    - Host for https://static.grinnell.edu - The server landing page
    - Host for https://vaf.grinnell.edu - The Visualizing Abolition and Freedom (VAF) static site
    - Host for https://vaf-kiosk.grinnell.edu - The VAF kiosk site
    - Host for https://rootstalk-static.grinnell.edu - The FUTURE Rootstalk ezine site

## To Initialize the Host

Launch Traefik, Portainer and WhoAmI like so:

```
sudo su
docker stop $(docker ps -q); docker system prune --force
docker network create web
cd /opt/docker-traefik2-host
docker-compose up -d; docker-compose logs
```

##  Launching Application Stacks On static.grinnell.edu

```
# static.grinnell.edu - The server landing page
NAME=static-landing-page
HOST=static.grinnell.edu
IMAGE="mcfatem/static-landing"
docker container run -d --name ${NAME} \
    --label traefik.backend=${NAME} \
    --label traefik.docker.network=web \
    --label "traefik.frontend.rule=Host:${HOST}" \
    --label traefik.port=80 \
    --label com.centurylinklabs.watchtower.enable=true \
    --network web \
    --restart always \
  ${IMAGE}

# vaf.grinnell.edu - The VAF site
docker container run -d --name vaf \
    --label traefik.backend=vaf \
    --label traefik.docker.network=web \
    --label "traefik.frontend.rule=Host:vaf.grinnell.edu" \
    --label traefik.port=80 \
    --label com.centurylinklabs.watchtower.enable=true \
    --network web \
    --restart always \
  mcfatem/vaf

# vaf-kiosk.grinnell.edu - The VAF kiosk site
docker container run -d --name vaf-kiosk \
    --label traefik.backend=vaf-kiosk \
    --label traefik.docker.network=web \
    --label "traefik.frontend.rule=Host:vaf-kiosk.grinnell.edu" \
    --label traefik.port=80 \
    --label com.centurylinklabs.watchtower.enable=true \
    --network web \
    --restart always \
  mcfatem/vaf-kiosk
```

## Attempting To Use Traefik v2
