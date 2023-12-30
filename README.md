"# bitnami-elk-stack" 

# Enhance the sysctl caps
```sudo echo "vm.max_map_count=262144" >> /etc/sysctl.d/elasticsearchSpecifications.conf && sudo sysctl --system```

# set the global variable for AltDNSNames
````DNS:elasticsearch-${DNAME},DNS:localhost,DNS:*.elasticsearch-${DNAME},DNS:elasticsearch-${DNAME}-0,DNS:elasticsearch-${DNAME}-1,IP:127.0.0.1"````

# Create a CA to be used for all certs
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
```
mkdir certs/kibana
cd certs/kibana/
openssl genrsa  -out rootCA.key 4096
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 36500 -out rootCA.crt
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
 -addext "subjectAltName=DNS:www.${NGINXDOMAIN},DNS:${NGINXDOMAIN}"

```


# Validate

```curl -u "elastic:Elastic123" --location https://192.168.1.230 -kL```



