# To Expose Dockerd API over Internet

## After successfully installation, start and enable the docker service.

### Verify the status of docker service using

```bash
sudo docker status docker.service
```

# output:

``` bash
systemctl status docker.service
```

#### docker.service - Docker Application Container Engine

### Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
``` text
     Active: active (running) since Thu 2024-11-14 06:05:25 UTC; 2min 26s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 965 (dockerd)
      Tasks: 14
     Memory: 107.6M
        CPU: 667ms
```

## Now note the path of docker systemd service file and open it.

```bash
sudo vim /lib/systemd/system/docker.service
```

[Service]

Type=notify

ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock


## Edit the docker.service 

```bash
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2376 --tlsverify --tlscacert=/etc/docker/certs/my-ca.pem --tlscert=/etc/docker/certs/server-cert.pem --tlskey=/etc/docker/certs/server-key.pem
```

## Certificates

As we can observe the above command, we need to place the `Certificate authority`, `Server Cert` and `Server Cert key` in `/etc/docker/certs` path

## Restart the Daemon and docker service

```bash
sudo systemctl daemon-reload
```

```bash
sudo docker restart docker.service
```

## Verify the docker service status

```bash
systemctl status docker.service      
```

#### docker.service - Docker Application Container Engine

### Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
``` text
     Active: active (running) since Thu 2024-11-14 06:05:25 UTC; 58min ago

TriggeredBy: ● docker.socket

       Docs: https://docs.docker.com

   Main PID: 965 (dockerd)

      Tasks: 14

     Memory: 107.2M

        CPU: 1.237s

     CGroup: /system.slice/docker.service

             └─965 /usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2376 --tlsverify --tlscacert=/etc/docker/certs/my-ca.pem --tlscert=/etc/docker/certs/server-cert.pem --tlskey=/etc/docker/certs/server-key.pem
```
## Verify the docker daemon port `(dockerd)` status

```bash
sudo netstat -tupln | grep dockerd
```

### Output :
```text
tcp6       0      0 :::2376                 :::*                    LISTEN      965/dockerd
```
# Thus Docker is exposed over the internet, now you can connect to the docker daemon by creating a context in the docker client env

```bash
docker context create remote --description "remote_tls_connection" --docker "host=tcp://docker_host_ip:2376,ca=/path/to/my-ca.pem,cert=/path/to/client-cert.pem,key=/path/to/client-key.pem"
````
```bash
docker context ls
````
```bash
docker context use remote
```
