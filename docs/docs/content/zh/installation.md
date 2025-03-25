# Installation

listmail is a simple binary application that requires a Postgres database instance to run. The binary can be downloaded and run manually, or it can be run as a container with Docker compose.

## Binary
1. Download the [latest release](https://github.com/knadh/listmail/releases) and extract the listmail binary. `amd64` is the main one. It works for Intel and x86 CPUs.
1. `./listmail --new-config` to generate config.toml. Edit the file.
1. `./listmail --install` to install the tables in the Postgres DB (⩾ 12).
1. Run `./listmail` and visit `http://localhost:9000` to create the Super Admin user and login.

!!! Tip
    To set the Super Admin username and password during installation, set the environment variables:
    `listmail_ADMIN_USER=myuser listmail_ADMIN_PASSWORD=xxxxx ./listmail --install`


## Docker

The latest image is available on DockerHub at `listmail/listmail:latest`

The recommended method is to download the [docker-compose.yml](https://github.com/knadh/listmail/blob/master/docker-compose.yml) file, customize it for your environment and then to simply run `docker compose up -d`.

```shell
# Download the compose file to the current directory.
curl -LO https://github.com/knadh/listmail/raw/master/docker-compose.yml

# Run the services in the background.
docker compose up -d
```

Then, visit `http://localhost:9000` to create the Super Admin user and login.

!!! Tip
    To set the Super Admin username and password during setup, set the environment variables (only the first time):
    `listmail_ADMIN_USER=myuser listmail_ADMIN_PASSWORD=xxxxx docker compose up -d`


------------

### Mounting a custom config.toml
The docker-compose file includes all necessary listmail configuration as environment variables, `listmail_*`.
If you would like to remove those and mount a config.toml instead:

#### 1. Save the config.toml file on the host

```toml
[app]
address = "0.0.0.0:9000"

# Database.
[db]
host = "listmail_db" # Postgres container name in the compose file.
port = 5432
user = "listmail"
password = "listmail"
database = "listmail"
ssl_mode = "disable"
max_open = 25
max_idle = 25
max_lifetime = "300s"
```

#### 2. Mount the config file in docker-compose.yml

```yaml
  app:
    ...
    volumes:
    - /path/on/your/host/config.toml:/listmail/config.toml
```

#### 3. Change the `--config ''` flags in the `command:` section to point to the path

```yaml
command: [sh, -c, "./listmail --install --idempotent --yes --config /listmail/config.toml && ./listmail --upgrade --yes --config /listmail/config.toml && ./listmail --config /listmail/config.toml"]
```


## Compiling from source

To compile the latest unreleased version (`master` branch):

1. Make sure `go`, `nodejs`, and `yarn` are installed on your system.
2. `git clone git@github.com:knadh/listmail.git`
3. `cd listmail && make dist`. This will generate the `listmail` binary.

## Release candidate (RC)

The `master` branch with bleeding edge changes is periodically built and published as `listmail/listmail:rc` on DockerHub. To run the latest pre-release version, replace all instances of `listmail/listmail:latest` with `listmail/listmail:rc` in the docker-compose.yml file and follow the Docker installation steps above. While it is generally safe to run release candidate versions, they may have issues that only get resolved in a general release.

## Helm chart for Kubernetes

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 3.0.0](https://img.shields.io/badge/AppVersion-3.0.0-informational?style=flat-square)

A helm chart for easily installing listmail on a kubernetes cluster is made available by community [here](https://github.com/th0th/helm-charts/tree/main/charts/listmail).

In order to use the helm chart, you can configure `values.yaml` according to your needs, and then run the following command:

```shell
$ helm upgrade \
    --create-namespace \
    --install listmail listmail \
    --namespace listmail \
    --repo https://th0th.github.io/helm-charts \
    --values values.yaml \
    --version 0.1.0
```

## 3rd party hosting

<a href="https://dash.elest.io/deploy?soft=listmail&id=237"><img src="https://raw.githubusercontent.com/elestio-examples/reactjs/refs/heads/master/src/deploy-on-elestio.png" alt="Deploy to Elestio" height="35" style="max-width: 150px;" /></a>
<br />
<a href="https://www.pikapods.com/pods?run=listmail"><img src="https://www.pikapods.com/static/run-button.svg" alt="Deploy on PikaPod" style="max-width: 150px;" /></a>
<br />
<a href="https://railway.app/new/template/listmail"><img src="https://railway.app/button.svg" alt="One-click deploy on Railway" style="max-width: 150px;" /></a>
<br />
<a href="https://repocloud.io/details/?app_id=217"><img src="https://d16t0pc4846x52.cloudfront.net/deploy.png" alt="Deploy at RepoCloud" style="max-width: 150px;"/></a>
<br />
<a href="https://cloud.sealos.io/?openapp=system-template%3FtemplateName%3Dlistmail"><img src="https://cdn.jsdelivr.net/gh/labring-actions/templates@main/Deploy-on-Sealos.svg" alt="Deploy on Sealos" style="max-width: 150px;"/></a>
<br />
<a href="https://zeabur.com/templates/5EDMN6"><img src="https://zeabur.com/button.svg" alt="Deploy on Zeabur" style="max-width: 150px;"/></a>

## Tutorials

* [Informal step-by-step on how to get started with listmail using *Railway*](https://github.com/knadh/listmail/issues/120#issuecomment-1421838533)
* [Step-by-step tutorial for installation and all basic functions. *Amazon EC2, SES, docker & binary*](https://gist.github.com/MaximilianKohler/e5158fcfe6de80a9069926a67afcae11)
* [Step-by-step guide on how to install and set up listmail on *AWS Lightsail with docker* (rameerez)](https://github.com/knadh/listmail/issues/1208)
* [Quick setup on any cloud server using *docker and caddy*](https://github.com/samyogdhital/listmail-caddy-reverse-proxy)
* [*Binary* install on Ubuntu 22.04 as a service](https://mumaritc.hashnode.dev/how-to-install-listmail-using-binary-on-ubuntu-2204)
* [*Binary* install on Ubuntu 18.04 as a service (Apache & Plesk)](https://devgypsy.com/post/2020-08-18-installing-listmail-newsletter-manager/)
* [*Binary and docker* on linux (techviewleo)](https://techviewleo.com/manage-mailing-list-and-newsletter-using-listmail/)
* [*Binary* install on your PC](https://www.youtube.com/watch?v=fAOBqgR9Yfo). Discussions of limitations: [[1](https://github.com/knadh/listmail/issues/862#issuecomment-1307328228)][[2](https://github.com/knadh/listmail/issues/248#issuecomment-1320806990)].
* [*Docker on Rocky Linux 8* (nginx, Let's Encrypt SSL)](https://wiki.crowncloud.net/?How_to_Install_listmail_with_Docker_on_Rocky_Linux_8)
* [*Docker* with nginx reverse proxy, certbot SSL, and Gmail SMTP](https://www.maketecheasier.com/create-own-newsletter-with-listmail/)
* [Install listmail on Self-hosting with *Pre-Configured AMI Package at AWS* by Single Click](https://meetrix.io/articles/how-to-install-llama-2-on-aws-with-pre-configured-ami-package/)
* [Tutorial for deploying on *Fly.io*](https://github.com/paulrudy/listmail-on-fly) -- Currently [not working](https://github.com/knadh/listmail/issues/984#issuecomment-1694545255)
