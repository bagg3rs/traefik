# Traefik v2 docker-compose example

This `Docker-Compose` file is used to configure Traefik, a popular reverse proxy and load balancer, in a Docker environment.
It includes support for DNS Challenge with Cloudflare, which automatically creates wildcard certificates for labelled containers.

<img width="1469" alt="image" src="https://user-images.githubusercontent.com/20583399/163053200-7ca3506a-eec0-4ec1-80d9-35645bf872f4.png">

Included example [LibreSpeed](https://github.com/librespeed/speedtest) container will automatically get certificates once the `.env.example` file is changed to `.env` with details of Cloudflare entered.

<img width="769" alt="image" src="https://user-images.githubusercontent.com/20583399/163053549-d759b3cd-6725-4151-b28c-90758a39d504.png">
