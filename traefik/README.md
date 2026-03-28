# Traefik

> [!IMPORTANT]
> Please go open the [GitLab repo](https://git.aio.sh/assie/docker/-/tree/main/traefik) to this template since you'd require a little more.

## Create domains
After installing the container it auto generated the folder `conf.d` in there you can add new domains

### Template

``` yaml
http:
  routers:
    SERVICE-NAME:
      rule: "Host(`sub.domain.com`)"
      entryPoints:
        - websecure
      service: SERVICE-NAME
      tls:
        certResolver: letsencrypt

  services:
    SERVICE-NAME:
      loadBalancer:
        servers:
          - url: "http://0.0.0.0:80"
```