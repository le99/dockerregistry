# Docker Registry with Composer, SSL but no auth

## Setup

```bash
mkdir certs
openssl req \
 -x509 -newkey rsa:4096 -days 365 -config openssl.conf \
 -keyout certs/domain.key -out certs/domain.crt
```

For every host that uses the registry use copy the `domain.crt` file to `/etc/docker/certs.d/my-registry.com:5000/ca.crt`

```bash
mkdir -p /etc/docker/certs.d/my-registry.com:5000
cp ./domain.crt /etc/docker/certs.d/my-registry.com\:5000/ca.crt
```

Also in `/etc/hosts` add a line with
```bash
<ip-registry> my-registry.com
```

## Run Registry
```bash
docker-composer up -d
```

## Interact with Registry
```bash
docker image tag hello-world localhost:5000/hello-world
docker push localhost:5000/hello-world
docker pull localhost:5000/hello-world
```

## Act as Mirror
In every docker host add to `/etc/docker/daemon.json`
```json
{
  "registry-mirrors": ["https://<my-docker-mirror-host>"]
}
```

Restart Docker
```bash
sudo systemctl restart docker
```

[https://docs.docker.com/registry/recipes/mirror/](https://docs.docker.com/registry/recipes/mirror/)

## Query images in registry

```bash
curl --cacert ./domain.crt -X GET https://my-registry.com:5000/v2/_catalog
```

## References
* [https://docs.docker.com/registry/deploying/](https://docs.docker.com/registry/deploying/)
* [https://docs.docker.com/registry/insecure/](https://docs.docker.com/registry/insecure/)
* [https://www.youtube.com/watch?v=r15S2tBevoE](https://www.youtube.com/watch?v=r15S2tBevoE)
* [https://github.com/docker/distribution/issues/948](https://github.com/docker/distribution/issues/948)
* [https://github.com/justmeandopensource/docker/tree/master/docker-compose-files/docker-registry](https://github.com/justmeandopensource/docker/tree/master/docker-compose-files/docker-registry)
*[https://stackoverflow.com/questions/31251356/how-to-get-a-list-of-images-on-docker-registry-v2](https://stackoverflow.com/questions/31251356/how-to-get-a-list-of-images-on-docker-registry-v2)
