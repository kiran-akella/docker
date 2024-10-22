openssl genrsa -out server-key.pem 2048
openssl req -new -key server-key.pem -subj '/CN=docker-host' -out server.csr
echo "extendedKeyUsage = serverAuth" >> extfile.cnf
echo subjectAltName = IP:(docker_host_ip),IP:127.0.0.1 >> extfile.cnf
openssl x509 -req -days 365 -in server.csr -CA my-ca.pem -CAkey my-ca-key.pem -CAcreateserial -extfile extfile.cnf -out server-cert.pem

openssl genrsa -out client-key.pem 2048
openssl req -new -key client-key.pem -subj '/CN=docker-client' -out client.csr
echo "extendedKeyUsage = clientAuth"  >> extfile_client.cnf
openssl x509 -req -days 365 -in client.csr -CA my-ca.pem -CAkey my-ca-key.pem -CAcreateserial -extfile extfile_client.cnf -out client-cert.pem

docker context create remote --description "remote_tls_connection" --docker "host=tcp://docker_host_ip:2376,ca=/path/to/my-ca.pem,cert=/path/to/client-cert.pem,key=/path/to/client-key.pem"
docker context ls
docker context use remote

