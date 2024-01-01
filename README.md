## bitnami-elk-stack

Reverse engineered from the helm based deployment model.



# Enhance the sysctl caps
Elasticsearch requires some of the sysctl caps increased. create and configure changes to be permanent across reboots.

```
sudo echo "vm.max_map_count=262144" >> /etc/sysctl.d/elasticsearchSpecifications.conf 
sudo sysctl --system
```

# set the global variable for AltDNSNames
Only if you need to keep things simpler.Sample certificate includes servicename and other SANs for inclusion.
```
DNSALTNAME="DNS:elasticsearch-${DNAME},DNS:localhost,DNS:*.elasticsearch-${DNAME},DNS:elasticsearch-${DNAME}-0,DNS:elasticsearch-${DNAME}-1,IP:127.0.0.1"
```

# create certs directory
```
mkdir certs
```


# Create a CA to be used for all certs

All the subsequent components inside the elasticsearch use same CA. Create a privateCA and use it to sign all CSRs


```
openssl genrsa  -out rootCA.key 4096
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 36500 -out rootCA.crt

```

# Run through loop to generate certs for all components -- also generate SSLs

```
for DNAME  in ingest data coordinating master 
do
openssl genrsa -out elasticsearch-${DNAME}.key 4096 
openssl req -new -sha256 \
 -key elasticsearch-${DNAME}.key \
 -subj "/C=US/ST=MI/O=namename, Inc./CN=elasticsearch-${DNAME}" \
 -reqexts SAN \
 -config <(cat /etc/ssl/openssl.cnf <(printf "\n[SAN]\nsubjectAltName=DNS:elasticsearch-${DNAME},DNS:localhost,DNS:*.elasticsearch-${DNAME},DNS:elasticsearch-${DNAME}-0,DNS:elasticsearch-${DNAME}-1,IP:127.0.0.1")) \
 -out elasticsearch-${DNAME}.csr


openssl req -in elasticsearch-${DNAME}.csr -noout -text
openssl x509 -req -extfile <(printf "subjectAltName=DNS:elasticsearch-${DNAME},DNS:localhost,DNS:*.elasticsearch-${DNAME},DNS:elasticsearch-${DNAME}-0,DNS:elasticsearch-${DNAME}-1,IP:127.0.0.1") -days 120 -in elasticsearch-${DNAME}.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out elasticsearch-${DNAME}.crt -sha256
done
```


# Prepare Kibana Certs
Kibana uses its own CA and certs. Also it need to include the coordinating node's CA + SSL + key which we have mounted in the docker-compose.yaml

```
mkdir certs/kibana
cd certs/kibana/
```
# generate CA for kibana only.
openssl genrsa  -out rootCA.key 4096
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 36500 -out rootCA.crt

# generate CERTs for Kibana
```
DNAME=kibana
openssl genrsa -out elasticsearch-${DNAME}.key 4096 

openssl req -new -sha256 \
 -key elasticsearch-${DNAME}.key \
 -subj "/C=US/ST=MI/O=namename, Inc./CN=elasticsearch-${DNAME}" \
 -reqexts SAN \
 -config <(cat /etc/ssl/openssl.cnf <(printf "\n[SAN]\nsubjectAltName=DNS:elasticsearch-${DNAME},DNS:localhost,DNS:*.elasticsearch-${DNAME},DNS:elasticsearch-${DNAME}-0,DNS:elasticsearch-${DNAME}-1,IP:127.0.0.1")) \
 -out elasticsearch-${DNAME}.csr

openssl req -in elasticsearch-${DNAME}.csr -noout -text
openssl x509 -req -extfile <(printf "subjectAltName=DNS:elasticsearch-${DNAME},DNS:localhost,DNS:*.elasticsearch-${DNAME},DNS:elasticsearch-${DNAME}-0,DNS:elasticsearch-${DNAME}-1,IP:127.0.0.1") -days 120 -in elasticsearch-${DNAME}.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out elasticsearch-${DNAME}.crt -sha256
```

# Nginx CERTS

To enable HTTPS in bitnami/nginx, creae SSL certs including all the SANs

```

NGINXDOMAIN=elk.yourapp.com
openssl req -x509 \
 -newkey rsa:4096 \
 -sha256 \
 -days 3650 \
 -nodes \
 -keyout ${NGINXDOMAIN}.key \
 -out ${NGINXDOMAIN}.crt \
 -subj "/CN=${NGINXDOMAIN}" \
 -addext "subjectAltName=DNS:www.${NGINXDOMAIN},DNS:*.${NGINXDOMAIN},DNS:someother.domain,IP:127.0.0.1"

```



# ignite the docker-compose.yml

```
cd ../
docker compose up -d
```

wait for all the pods to warm up

```
docker ps -a

```


# Validate
send a GET call to the elasticsearch with the username/password for validation.

```
curl -u "elastic:Elastic123" --location https://192.168.1.230 -kL
```

You should get a response from the elasticsearch.


