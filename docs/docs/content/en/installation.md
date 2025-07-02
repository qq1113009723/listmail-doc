# Installation

stmails is a simple binary application.

## Binary Installation
1. Download the [latest release](https://github.com/knadh/stmails/releases) and extract the stmails binary. `amd64` is the main one. It works for Intel and x86 CPUs.
2. **Important:** Set your license key in the config.toml file. This is required to run the application.
   ```toml
   [app]
   license_key = "YOUR_LICENSE_KEY"
   ```
3. Run `./stmails` and visit `http://localhost:9000` to create the Super Admin user and login.

## Docker

The latest image is available on DockerHub at `stmails/stmails:latest`

The recommended method is to download the [docker-compose.yml](https://github.com/knadh/stmails/blob/master/docker-compose.yml) file, customize it for your environment and then to simply run `docker compose up -d`.

```shell
# Download the compose file to the current directory.
curl -LO https://github.com/knadh/stmails/raw/master/docker-compose.yml

# Run the services in the background.
docker compose up -d
```

Then, visit `http://localhost:9000` to create the Super Admin user and login.

!!! Tip
    To set the Super Admin username and password during setup, set the environment variables (only the first time):
    `stmails_ADMIN_USER=myuser stmails_ADMIN_PASSWORD=xxxxx docker compose up -d`


------------

### Mounting a custom config.toml
The docker-compose file includes all necessary stmails configuration as environment variables, `stmails_*`.
If you would like to remove those and mount a config.toml instead:

#### 1. Save the config.toml file on the host

```toml
[app]
address = "0.0.0.0:9000"

# Database.
[db]
host = "stmails_db" # Postgres container name in the compose file.
port = 5432
user = "stmails"
password = "stmails"
database = "stmails"
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
    - /path/on/your/host/config.toml:/stmails/config.toml
```

#### 3. Change the `--config ''` flags in the `command:` section to point to the path

```yaml
command: [sh, -c, "./stmails --install --idempotent --yes --config /stmails/config.toml && ./stmails --upgrade --yes --config /stmails/config.toml && ./stmails --config /stmails/config.toml"]
```


## Compiling from source

To compile the latest unreleased version (`master` branch):

1. Make sure `go`, `nodejs`, and `yarn` are installed on your system.
2. `git clone git@github.com:knadh/stmails.git`
3. `cd stmails && make dist`. This will generate the `stmails` binary.

## Release candidate (RC)

The `master` branch with bleeding edge changes is periodically built and published as `stmails/stmails:rc` on DockerHub. To run the latest pre-release version, replace all instances of `stmails/stmails:latest` with `stmails/stmails:rc` in the docker-compose.yml file and follow the Docker installation steps above. While it is generally safe to run release candidate versions, they may have issues that only get resolved in a general release.

## Helm chart for Kubernetes

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 3.0.0](https://img.shields.io/badge/AppVersion-3.0.0-informational?style=flat-square)

A helm chart for easily installing stmails on a kubernetes cluster is made available by community [here](https://github.com/th0th/helm-charts/tree/main/charts/stmails).

In order to use the helm chart, you can configure `values.yaml` according to your needs, and then run the following command:

```shell
$ helm upgrade \
    --create-namespace \
    --install stmails stmails \
    --namespace stmails \
    --repo https://th0th.github.io/helm-charts \
    --values values.yaml \
    --version 0.1.0
```

## 3rd party hosting

<a href="https://dash.elest.io/deploy?soft=stmails&id=237"><img src="https://raw.githubusercontent.com/elestio-examples/reactjs/refs/heads/master/src/deploy-on-elestio.png" alt="Deploy to Elestio" height="35" style="max-width: 150px;" /></a>
<br />
<a href="https://www.pikapods.com/pods?run=stmails"><img src="https://www.pikapods.com/static/run-button.svg" alt="Deploy on PikaPod" style="max-width: 150px;" /></a>
<br />
<a href="https://railway.app/new/template/stmails"><img src="https://railway.app/button.svg" alt="One-click deploy on Railway" style="max-width: 150px;" /></a>
<br />
<a href="https://repocloud.io/details/?app_id=217"><img src="https://d16t0pc4846x52.cloudfront.net/deploy.png" alt="Deploy at RepoCloud" style="max-width: 150px;"/></a>
<br />
<a href="https://cloud.sealos.io/?openapp=system-template%3FtemplateName%3Dstmails"><img src="https://cdn.jsdelivr.net/gh/labring-actions/templates@main/Deploy-on-Sealos.svg" alt="Deploy on Sealos" style="max-width: 150px;"/></a>
<br />
<a href="https://zeabur.com/templates/5EDMN6"><img src="https://zeabur.com/button.svg" alt="Deploy on Zeabur" style="max-width: 150px;"/></a>

## Tutorials

* [Informal step-by-step on how to get started with stmails using *Railway*](https://github.com/knadh/stmails/issues/120#issuecomment-1421838533)
* [Step-by-step tutorial for installation and all basic functions. *Amazon EC2, SES, docker & binary*](https://gist.github.com/MaximilianKohler/e5158fcfe6de80a9069926a67afcae11)
* [Step-by-step guide on how to install and set up stmails on *AWS Lightsail with docker* (rameerez)](https://github.com/knadh/stmails/issues/1208)
* [Quick setup on any cloud server using *docker and caddy*](https://github.com/samyogdhital/stmails-caddy-reverse-proxy)
* [*Binary* install on Ubuntu 22.04 as a service](https://mumaritc.hashnode.dev/how-to-install-stmails-using-binary-on-ubuntu-2204)
* [*Binary* install on Ubuntu 18.04 as a service (Apache & Plesk)](https://devgypsy.com/post/2020-08-18-installing-stmails-newsletter-manager/)
* [*Binary and docker* on linux (techviewleo)](https://techviewleo.com/manage-mailing-list-and-newsletter-using-stmails/)
* [*Binary* install on your PC](https://www.youtube.com/watch?v=fAOBqgR9Yfo). Discussions of limitations: [[1](https://github.com/knadh/stmails/issues/862#issuecomment-1307328228)][[2](https://github.com/knadh/stmails/issues/248#issuecomment-1320806990)].
* [*Docker on Rocky Linux 8* (nginx, Let's Encrypt SSL)](https://wiki.crowncloud.net/?How_to_Install_stmails_with_Docker_on_Rocky_Linux_8)
* [*Docker* with nginx reverse proxy, certbot SSL, and Gmail SMTP](https://www.maketecheasier.com/create-own-newsletter-with-stmails/)
* [Install stmails on Self-hosting with *Pre-Configured AMI Package at AWS* by Single Click](https://meetrix.io/articles/how-to-install-llama-2-on-aws-with-pre-configured-ami-package/)
* [Tutorial for deploying on *Fly.io*](https://github.com/paulrudy/stmails-on-fly) -- Currently [not working](https://github.com/knadh/stmails/issues/984#issuecomment-1694545255)
