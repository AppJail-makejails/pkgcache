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

## Acknowledgments

This Makejail was not created without the help of

* [sko](https://forums.freebsd.org/members/sko.49061/): [Local Cache for PKGs?](https://forums.freebsd.org/threads/local-cache-for-pkgs.60859/#post-365296)
