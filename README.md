# pkgcache

Makejail to use NGINX as a pkg cache mirror.

docs.nginx.com/nginx/admin-guide/content-cache/content-caching

<img src="https://cdn2.iconfinder.com/data/icons/whcompare-isometric-web-hosting-servers/50/database-cache-512.png" width="30%" height="auto" />

## How to use this Makejail

### Basic usage

```sh
appjail makejail -j pkgcache -f gh+AppJail-makejails/pkgcache \
	-o virtualnet=":pkgcache default" 	
	-o nat
# See note #1
service appjail-health restart
```

### Using the mirror

To speed up the installation of packages in your new jails, you can configure each jail in your Makejail to take advantage of your pkg mirror.

```
# appjail jail list -j pkgcache
STATUS  NAME       TYPE  VERSION       PORTS  NETWORK_IP4
UP      pkgcache   thin  13.2-RELEASE  -      10.0.0.2
```

**Mirror.conf**

```
FreeBSD: {
  url: "http://10.0.0.2/${ABI}/latest",
  mirror_type: "http",
  signature_type: "fingerprints",
  fingerprints: "/usr/share/keys/pkg",
  enabled: yes
}
```

**pkg.makejail**:

```
CMD mkdir -p /usr/local/etc/pkg/repos
COPY Mirror.conf /usr/local/etc/pkg/repos
```

Include the following in your Makejail:

```
INCLUDE pkg.makejail
```

### Arguments

* `pkgcache_worker_processes` (default: `auto`): see [worker\_processes](https://nginx.org/en/docs/ngx_core_module.html#worker_processes).
* `pkgcache_worker_connections` (default: `1024`): see [worker\_connections](https://nginx.org/en/docs/ngx_core_module.html#worker_connections).
* `pkgcache_zone_size` (default: `60m`): see [proxy\_cache\_path](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache_path).
* `pkgcache_inactive` (default: `120m`): see [proxy\_cache\_path](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache_path).
* `pkgcache_cache_valid` (default: `1d`): see [proxy\_cache\_valid](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache_valid).

### Volumes

| Name              | Owner | Group | Perm | Type | Mountpoint                 |
| ----------------- | ----- | ----- | ---- | ---- | -------------------------- |
| pkgcache-cachedir | 80    | 80    |  -   |  -   | /var/cache/nginx/pkgcache  |

## Acknowledgments

This Makejail was not created without the help of

* [sko](https://forums.freebsd.org/members/sko.49061/): [Local Cache for PKGs?](https://forums.freebsd.org/threads/local-cache-for-pkgs.60859/#post-365296)

## Notes

1. A healthchecker is used to check and restart NGINX if necessary.
