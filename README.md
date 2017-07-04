# caddy

A [Docker](http://docker.com) image for [Caddy](http://caddyserver.com). This image includes the following plugins:

* [git](http://caddyserver.com/docs/http.git) plugin.
* [cors](http://caddyserver.com/docs/http.cors) plugin.
* [expires](http://caddyserver.com/docs/http.expires) plugin.
* [jwt](http://caddyserver.com/docs/http.jwt) plugin.
* [minify](http://caddyserver.com/docs/http.minify) plugin.
* [prometheus](http://caddyserver.com/docs/http.prometheus) plugin.
* [ratelimit](http://caddyserver.com/docs/http.ratelimit) plugin.

Plugins can be configured via the `plugins` build arg.

[![](https://images.microbadger.com/badges/image/syndesis/caddy.svg)](https://microbadger.com/images/syndesis/caddy "Get your own image badge on microbadger.com")

## Getting Started

```sh
$ docker run -d -p 2015:2015 syndesis/caddy
```

Point your browser to `http://127.0.0.1:2015`.

> Be aware! If you don't bind mount the location certificates are saved to, you may hit Let's Encrypt rate [limits](https://letsencrypt.org/docs/rate-limits/) rending further certificate generation or renewal disallowed (for a fixed period)! See "Saving Certificates" below!

### Saving Certificates

Save certificates on host machine to prevent regeneration every time container starts.
Let's Encrypt has [rate limit](https://community.letsencrypt.org/t/rate-limits-for-lets-encrypt/6769).
```sh
$ docker run -d \
    -v $(pwd)/Caddyfile:/etc/Caddyfile \
    -v $HOME/.caddy:/root/.caddy \
    -p 80:80 -p 443:443 \
    syndesis/caddy
```


Here, `/root/.caddy` is the location *inside* the container where caddy will save certificates.

Additionally, you can use an *environment variable* to define the exact location caddy should save generated certificates:

```sh
$ docker run -d \
    -e "CADDYPATH=/etc/caddycerts" \
    -v $HOME/.caddy:/etc/caddycerts \
    -p 80:80 -p 443:443 \
    syndesis/caddy
```

Above, we utilize the `CADDYPATH` environment variable to define a different location inside the container for
certificates to be stored. This is probably the safest option as it ensures any future docker image changes don't
interfere with your ability to save certificates!

### Using git sources

Caddy can serve sites from git repository using [git](https://caddyserver.com/docs/git) plugin.

##### Create Caddyfile

Replace `github.com/syndesis/webtest` with your repository.

```sh
$ printf "0.0.0.0\nroot src\ngit github.com/syndesis/webtest" > Caddyfile
```

##### Run the image

```sh
$ docker run -d -v $(pwd)/Caddyfile:/etc/Caddyfile -p 2015:2015 syndesis/caddy
```
Point your browser to `http://127.0.0.1:2015`.

## Usage

#### Default Caddyfile

The image contains a default Caddyfile.

```
0.0.0.0
log stdout
errors stdout
gzip
prometheus
```

#### Paths in container

Caddyfile: `/etc/Caddyfile`

Sites root: `/srv`

#### Using local Caddyfile and sites root

Replace `/path/to/Caddyfile` and `/path/to/sites/root` accordingly.

```sh
$ docker run -d \
    -v /path/to/sites/root:/srv \
    -v path/to/Caddyfile:/etc/Caddyfile \
    -p 2015:2015 \
    syndesis/caddy
```

### Let's Encrypt Auto SSL
**Note** that this does not work on local environments.

Use a valid domain and add email to your Caddyfile to avoid prompt at runtime.
Replace `mydomain.com` with your domain and `user@host.com` with your email.
```
mydomain.com
tls user@host.com
```

##### Run the image

You can change the the ports if ports 80 and 443 are not available on host. e.g. 81:80, 444:443

```sh
$ docker run -d \
    -v $(pwd)/Caddyfile:/etc/Caddyfile \
    -p 80:80 -p 443:443 \
    syndesis/caddy
```
