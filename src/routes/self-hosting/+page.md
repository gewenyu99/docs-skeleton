Appwrite was designed from the ground up with self-hosting in mind. You can install and run Appwrite on any operating system that can run a [Docker CLI](https://www.docker.com/products/docker-desktop). Self-hosted Appwrite instances can be configured flexibly with access to the same features found on Appwrite Cloud.

## System Requirements

Appwrite is designed to run well on both small and large deployments. The minimum requirements to run Appwrite are as little as **1 CPU core** and **2GB of RAM**, and an operating system that supports Docker.

Appwrite requires [Docker Compose Version 2](https://docs.docker.com/compose/install/). To install Appwrite, make sure your Docker installation is updated to support Composer V2.

### Upgrading From Older Versions
If you are migrating from an older version of Appwrite, you need to follow the [migration instructions](/docs/upgrade).

## Install with Docker

The easiest way to start running your Appwrite server is by running our Docker installer tool from your terminal. Before running the installation command, make sure you have [Docker CLI](https://www.docker.com/products/docker-desktop) installed on your host machine.

You will be prompted to configure the following during the setup command:
1. Your Appwrite instance's HTTP and HTTPS ports.
2. Your Appwrite instance's secret key which used to encrypt sensitive data.
3. Your Appwrite instance's main hostname. Appwrite will generate a certificate using this hostname.
4. Your Appwrite instance's DNS A record hostname. Typically set to the same value as your Appwrite instance's hostname.

### Unix

```bash
docker run -it --rm \
    --volume /var/run/docker.sock:/var/run/docker.sock \
    --volume "$(pwd)"/appwrite:/usr/src/code/appwrite:rw \
    --entrypoint="install" \
    appwrite/appwrite:<?php echo APP_VERSION_STABLE; ?>
```

### Windows

Hyper-V and Containers Windows features must be enabled to run Appwrite on Windows with Docker. If you don't have these features available, you can install [Docker Desktop](https://docs.docker.com/desktop/windows/install/) which uses Virtualbox to run Appwrite on a Virtual Machine.

- CMD
```bash
docker run -it --rm ^
    --volume //var/run/docker.sock:/var/run/docker.sock ^
    --volume "%cd%"/appwrite:/usr/src/code/appwrite:rw ^
    --entrypoint="install" ^
    appwrite/appwrite:<?php echo APP_VERSION_STABLE; ?>
```

- PowerShell
```bash
docker run -it --rm `
    --volume /var/run/docker.sock:/var/run/docker.sock `
    --volume ${pwd}/appwrite:/usr/src/code/appwrite:rw `
    --entrypoint="install" `
    appwrite/appwrite:<?php echo APP_VERSION_STABLE; ?>
```

## One-Click Setups

In addition to running Appwrite locally, you can also launch Appwrite using a pre-configured setup. This allows you to get up and running with Appwrite quickly without installing Docker on your local machine.

Choose from one of the providers below:

- DigitalOcean
[Click to Install](https://marketplace.digitalocean.com/apps/appwrite)

- Gitpod
[Click to Install](https://gitpod.io/#https://github.com/appwrite/integration-for-gitpod)

- Akamai Compute
[Click to Install](https://www.linode.com/marketplace/apps/appwrite/appwrite/)

## Next Steps

Self-hosting Appwrite gives you more configurable options. You can customize Appwrite with your choice of S3 compatible storage adaptors, email and SMS providers, functions runtimes, and more.

[Learn about configuring Appwrite](/docs/configuration)

Self-hosted Appwrite instances can be made production ready. To run Appwrite successfully in a production environment, you should follow a few basic concepts and best practices.

[Learn about Appwrite in production](/docs/production)

## Manual (using docker-compose.yml)

For advanced Docker users, the manual installation might seem more familiar. To set up Appwrite manually, download the Appwrite base [docker-compose.yml](/install/compose) and [.env](/install/env) files, then move them inside a directory named `appwrite`. After the download completes, update the different environment variables as you wish in the `.env` file and start the Appwrite stack using the following Docker command:

```bash
docker compose up -d --remove-orphans
```

Once the Docker installation completes, go to your machine's hostname or IP address on your browser to access the Appwrite console. Please note that on hosts that are not Linux-native, the server might take a few minutes to start after installation completes.

## Stop

You can stop your Appwrite containers by using the following command executed from the same directory as your `docker-compose.yml` file.

```bash
docker compose stop
```

## Uninstall

To stop and remove your Appwrite containers, you can use the following command executed from the same directory as your `docker-compose.yml` file.

```bash
docker compose down -v
```
