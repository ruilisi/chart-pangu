# Chart-Pangu

**Chart Pangu** is a helm chart template which inherits from the default template, but brings many conveniences originated from our daily devops.
You can safely use it as the starting point of your helm chart since it is well tested.

> Pangu is the first living being and the creator of all in some versions of Chinese mythology[<sup>1</sup>](#refer-pangu).

## How to use it
```bash
git clone http://github.com/ruilisi/helm-chart-pangu
helm upgrade --install helm-chart-pangu -f PROJECT_VALUES.yaml
```

Example of `PROJECT_VALUES.yaml`

```yaml
image:
  repository: YOUR_IMAGE

env:
  PGUSER: postgres
  PGPASS: YOUR_PG_PASSWORD
  PGHOST: postgresql

ingress:
  enabled: true
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
  tls_all: true
  paths:
    - '/ping'
    - '/login'
    - '/logout'
  hosts:
    - host: api.AAA.com
    - host: api.BBB.com
    - host: api.CCC.com

replicaCount: 2

volumes:
  - name: redis-data
    persistentVolumeClaim:
      claimName: redis-data-redis-master-0
volumeMounts:
  - name: redis-data
    mountPath: /mnt/redis-data
```

## Configuraitons

#### Probes
This chart demonstrates a simple configuration for liveness/readiness probe with http get call to `/ping` and expects plain text `pong` to be returned.

#### Ingress

**Ingress certificates**

There are two ways to configure ingress certificates with this chart.
* Specify each secret separately with its hosts. In this way, each secret name is matched with different group of hosts.
```yaml
ingress:
  tls:
    - secretName: AAA
      hosts:
        - api.AAA.com
    - secretName: BBB
      hosts:
        - api.BBB1.com
        - api.BBB2.com
    - secretName: CCC
      hosts:
        - api.CCC1.com
        - api.CCC2.com
        - api.CCC3.com
```
* Specify secrets all at once by setting `tls_all` to `true` and put all domain names in a single array. In this way, each secret name is the same as the name of the associated host.
```yaml
ingress:
  ...
  tls_all: true
  hosts:
    - host: api.AAA.com
    - host: api.BBB.com
    - host: api.CCC.com
```

**Ingress paths**

There are two ways to configure ingress paths with this chart.
* Specify paths for each host one at a time.
```yaml
ingress:
  hosts:
    - host: api.AAA.com
      paths: 
        - '/ping'
        - '/login'
        - '/logout'
    - host: api.BBB1.com
      paths: 
        - '/ping'
        - '/login'
        - '/logout'
    - host: api.BBB2.com
      paths: 
        - '/ping'
        - '/login'
        - '/logout'
    - host: api.CCC1.com
      paths: 
        - '/ping'
        - '/login'
        - '/logout'
```

* Specify paths for each host all in one place if paths are the same.
```yaml
ingress:
  paths: 
    - '/ping'
    - '/login'
    - '/logout'
  hosts:
    - host: api.AAA.com
    - host: api.BBB1.com
    - host: api.BBB2.com
    - host: api.CCC1.com
```

The above two ways can be mixed in order to achive a short, clean but accurate configuration for ingress paths.

#### Persistent Volume 

* Specify a volume with a persistent volume claim.
```yaml
volumes:
  - name: redis-data
    persistentVolumeClaim:
      claimName: redis-data-redis-master-0
```

* Mount a local path against a volume.
```
volumeMounts:
  - name: redis-data
    mountPath: /mnt/redis-data
```

## Parameters

The following table lists the configurable parameters of the Redis chart and their default values.

| Parameter                                     | Description                                                                                                                                         | Default                                                 |
|-----------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------|
| `replicaCount`                                | replications for deployed application                                                                                                               | `1`                                                     |
| `image.tag`                                   |                                                                                                                                                     | `latest`                                                |
| `image.pullPolicy`                            |                                                                                                                                                     | `always`                                                |
| `nameOverride`                                | chart name that overrides the default one                                                                                                           | `""`                                                    |
| `fullnameOverride`                            | full chart name that overrides the default one                                                                                                      | `""`                                                    |
| `service.type`                                |                                                                                                                                                     | `ClusterIP`                                             |
| `service.port`                                |                                                                                                                                                     | `80`                                                    |
| `livenessProbe`                               |                                                                                                                                                     |  Described in above                                     |
| `readinessProbe`                              |                                                                                                                                                     |  Described in above                                     |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install helm-chart-pangu -f  PROJECT_VALUES.yaml \
  --set replicaCount=2
```

## Reference
<div id="refer-pangu"></div>

- [1] [Wiki of Pangu](https://en.wikipedia.org/wiki/Pangu)
