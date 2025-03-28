```bash
openssl genrsa -out server-key.pem 2048
```
```bash
openssl req -new -key server-key.pem -subj '/CN=docker-host' -out server.csr
```
```bash
echo "extendedKeyUsage = serverAuth" >> extfile.cnf
````
```bash
echo subjectAltName = IP:(docker_host_ip),IP:127.0.0.1 >> extfile.cnf
````
```bash
openssl x509 -req -days 365 -in server.csr -CA my-ca.pem -CAkey my-ca-key.pem -CAcreateserial -extfile extfile.cnf -out server-cert.pem
```
```bash
openssl genrsa -out client-key.pem 2048
````
```bash
openssl req -new -key client-key.pem -subj '/CN=docker-client' -out client.csr
````
```bash
echo "extendedKeyUsage = clientAuth"  >> extfile_client.cnf
````
```bash
openssl x509 -req -days 365 -in client.csr -CA my-ca.pem -CAkey my-ca-key.pem -CAcreateserial -extfile extfile_client.cnf -out client-cert.pem
```
```bash
docker context create remote --description "remote_tls_connection" --docker "host=tcp://docker_host_ip:2376,ca=/path/to/my-ca.pem,cert=/path/to/client-cert.pem,key=/path/to/client-key.pem"
````
```bash
docker context ls
````
```bash
docker context use remote
```
