# free5GC compose

This repository is a docker compose version of [free5GC](https://github.com/free5gc/free5gc) for stage 3. It's inspired by [free5gc-docker-compose](https://github.com/calee0219/free5gc-docker-compose) and also reference to [docker-free5gc](https://github.com/abousselmi/docker-free5gc).

You can setup your own config in [config](./config) folder and [docker-compose.yaml](docker-compose.yaml)

## Prerequisites

- [GTP5G kernel module](https://github.com/free5gc/gtp5g): needed to run the UPF (Currently, UPF only supports GTP5G versions 0.9.16 (use git clone --branch v0.9.16 --depth 1 https://github.com/free5gc/gtp5g.git).)
- [Docker Engine](https://docs.docker.com/engine/install): needed to run the Free5GC containers
- [Docker Compose v2](https://docs.docker.com/compose/install): needed to bootstrap the free5GC stack

## Start free5gc

Because we need to create tunnel interface, we need to use privileged container with root permission.

### Pull docker images from Docker Hub

```bash
docker compose pull
```

```bash
docker rmi $(docker images -f "dangling=true" -q)
```

### Run free5GC

You can create free5GC containers based on docker hub images:

```bash
# use images from docker hub
docker compose up # add -d to run in background mode
```

Destroy the established container resource after testing:

```bash
# Remove established containers (remote images)
docker compose rm
```

## Troubleshooting

Please refer to the [Troubleshooting](./TROUBLESHOOTING.md) for more troubleshooting information.

## Integration with (external) gNB/UE

#### Run UE inside gNB container

You can launch a UE using:

```console
docker exec -it ueransim bash
root@host:/ueransim# ./nr-ue -c config/uecfg.yaml


## Prometheous & Grafana

To start the core with Prometheous and Grafana, we need external compose service file to start with our core compose:

```bash
docker compose -f docker-compose.yaml -f docker-compose-prometheus.yaml up
```

Please make sure the metrics secions are enabled in NFs' config, it is disabled in default:

```yaml
  # Metrics configuration
  # If using the same bindingIPv4 as the sbi server, make sure that the ports are different
  metrics:
=>  enable: true # (Optional, default false)
    scheme: http # (Required) the protocol for metrics (http or https, default https)
    bindingIPv4: amf.free5gc.org # (Required) IP used to bind the metrics endpoint (default 0.0.0.0)
    port: 9091 # (Optional, default 9091) port used to bind the service
    tls: # (Optional) the local path of TLS key (Could be the same as the sbi ones)
      pem: cert/amf.pem # AMF TLS Certificate
      key: cert/amf.key # AMF TLS Private key
    namespace: free5gc # (Optional, default free5gc)
```

## Reference

- https://github.com/open5gs/nextepc/tree/master/docker
- https://github.com/abousselmi/docker-free5gc
