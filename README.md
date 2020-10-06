# Pangu Chart

**Pangu Chart** is a helm chart template which inherits from the default template, but brings many conveniences originated from our daily devops.
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
    - '/'
  hosts:
    - host: api.AAA.com
    - host: api.BBB.com
    - host: api.CCC.com
livenessProbe:
  enabled: false
readinessProbe:
  enabled: false

replicaCount: 1
volumes:
  - name: redis-data
    persistentVolumeClaim:
      claimName: redis-data-redis-master-0
volumeMounts:
  - name: redis-data
    mountPath: /mnt/redis-data
replicaCount: 1
```

## Reference
<div id="refer-pangu"></div>
- [1] [Wiki of Pangu](https://en.wikipedia.org/wiki/Pangu)
