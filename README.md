# Kubernetes deployment of calibre-web

This is a **Kubernetes** deployment of [calibre-web](https://github.com/janeczku/calibre-web) application which uses [linuxserver/calibre-web](https://hub.docker.com/r/linuxserver/calibre-web) docker image.

## How to

**Note** This deployment assumes the Kubernetes cluster runs the [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx) as ingress controller. If you use another ingress controller, you will have to adapt `ingress.yml` accordingly.

    git clone https://github.com/devtud/calibre-kubernetes

Edit `ingress.yml` and replace your domain name on this line:

```yml
  - host: <your-domain>
```

Open `deployment.yml` and adapt the two values on the following lines according to your needs (the user id and group id which own the files on the host machine - **do not set it as root!**).

```yml
            - name: PUID
              value: "1001"
            - name: PGID
              value: "1001"
```

Apply the Kubernetes resources:

    kubectl apply -f ns.yml
    kubectl apply -f pv_books.yml
    kubectl apply -f pv_config.yml
    kubectl apply -f pvc_books.yml
    kubectl apply -f pvc_config.yml
    kubectl apply -f deployment.yml
    kubectl apply -f service.yml
    kubectl apply -f ingress.yml


**Important** Even though you can access the deployed `calibre-web` app at your address, you won't be able to use it, because neither the calibre-web app nor the docker image provide a Calibre database. You have to get a Calibre database first and upload it to `/var/data/calibre/books/metadata.db` (note that this is the path on your host machine where the persistent volume [pv_books.yml](pv_books.yml) is pointing to). One way of doing this is to install a fresh Calibre on your computer and copy the `metadata.db` file from your computer to the mentioned path.

After you open the web interface, in the config screen put the following values:
- `/books` for the db path
- `/usr/bin/ebook-convert` for the converter path
- `/usr/bin/unrar` for the unrar path
