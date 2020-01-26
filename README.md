# Private Docker Registry
How to setup your own private docker registry for your docker images with [Digitalocean droplets](https://m.do.co/c/5fb9f23e93fe).
_This setup can be done on any linux servers._

## Installation

### Digitalocean
For this project we use a server from [Digitalocean droplets](https://m.do.co/c/5fb9f23e93fe). Create a droplet with a [Docker image](https://marketplace.digitalocean.com/apps/docker) from the Marketplace.
_Add your ssh key to this droplet to enter console easily._

#### Server configuration
Allow traffic via port 443 to your server by adding the firewall rule.
```bash
$ ufw allow '443/tcp'
```

Install the two packages for creating the ssl certifcates/keys for your domain and the password.
```bash
$ apt-get update && apt-get install certbot apache2-utils
```

Create the following directories where the certificates and password will be saved.
```bash
$ mkdir certs auth
```

Create your ssl domain certificate.
```bash
$ certbot certonly --standalone --email name@domain.ext -d name.domain.ext
```
_Replace the **name@domain.ext** to your email address and the **name.domain.ext** to your server._

After creating the ssl domain certificates, we have to copy the files to make it actually work.
Copy the private key.
```bash
$ cp /etc/letsencrypt/live/name.domain.ext/privkey.pem ./certs/domain.key 
```

Copy the content of the cert.pem and chain.pem files into one file.
```bash
$ cat /etc/letsencrypt/live/name.domain.ext/cert.pem /etc/letsencrypt/live/name.domain.ext/chain.pem > ./certs/domain.crt
```

Create your ssl domain password.
```bash
$ htpasswd -Bc auth/registry.password username
```
Choose your own **username** and fill in your password.

Copy the `docker-compose.yml` file as in this repository.
```bash
REGISTRY_HTTP_SECRET: ''
```
Fill in your http secret. This can be a random hex.

Run the `docker-compose.yml` file with detached so it can be runned on the background.
```bash
$ docker-compose up -d
```


## Credits and acknowledgements

* Author: [Kiet Tran][link-author]

Also see the list of [contributors][link-contributors] who participated in this project.

## License
The docker-registry is licensed under the MIT License. Please see the [LICENSE file][link-license] for details.

[link-version]: https://packagist.org/packages/squaremonks/docker-registry
[link-license]: LICENSE
[link-author]: https://github.com/kiettran
[link-contributors]: https://github.com/squaremonks/docker-registry/contributors
