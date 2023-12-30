"# bitnami-elk-stack" 
#sudo echo "vm.max_map_count=262144" >> /etc/sysctl.d/elasticsearchSpecifications.conf && sudo sysctl --system

# DNS:elasticsearch-${DNAME},DNS:localhost,DNS:*.elasticsearch-${DNAME},DNS:elasticsearch-${DNAME}-0,DNS:elasticsearch-${DNAME}-1,IP:127.0.0.1"

      
# openssl genrsa  -out rootCA.key 4096
# openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 36500 -out rootCA.crt

# for DNAME  in ingest data coordinating master 
# do

# openssl genrsa -out elasticsearch-${DNAME}.key 4096 
 
# openssl req -new -sha256 \
# -key elasticsearch-${DNAME}.key \
# -subj "/C=US/ST=MI/O=namename, Inc./CN=elasticsearch-${DNAME}" \
# -reqexts SAN \
# -config <(cat /etc/ssl/openssl.cnf <(printf "\n[SAN]\nsubjectAltName=DNS:elasticsearch-${DNAME},DNS:localhost,DNS:*.elasticsearch-${DNAME},DNS:elasticsearch-${DNAME}-0,DNS:elasticsearch-${DNAME}-1,IP:127.0.0.1")) \
# -out elasticsearch-${DNAME}.csr


# openssl req -in elasticsearch-${DNAME}.csr -noout -text
# openssl x509 -req -extfile <(printf "subjectAltName=DNS:elasticsearch-${DNAME},DNS:localhost,DNS:*.elasticsearch-${DNAME},DNS:elasticsearch-${DNAME}-0,DNS:elasticsearch-${DNAME}-1,IP:127.0.0.1") -days 120 -in elasticsearch-${DNAME}.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out elasticsearch-${DNAME}.crt -sha256
# done


# mkdir certs/kibana
# cd certs/kibana/
# openssl genrsa  -out rootCA.key 4096
# openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 36500 -out rootCA.crt
# DNAME=kibana
# openssl genrsa -out elasticsearch-${DNAME}.key 4096 

# openssl req -new -sha256 \
# -key elasticsearch-${DNAME}.key \
# -subj "/C=US/ST=MI/O=namename, Inc./CN=elasticsearch-${DNAME}" \
# -reqexts SAN \
# -config <(cat /etc/ssl/openssl.cnf <(printf "\n[SAN]\nsubjectAltName=DNS:elasticsearch-${DNAME},DNS:localhost,DNS:*.elasticsearch-${DNAME},DNS:elasticsearch-${DNAME}-0,DNS:elasticsearch-${DNAME}-1,IP:127.0.0.1")) \
# -out elasticsearch-${DNAME}.csr

# openssl req -in elasticsearch-${DNAME}.csr -noout -text
# openssl x509 -req -extfile <(printf "subjectAltName=DNS:elasticsearch-${DNAME},DNS:localhost,DNS:*.elasticsearch-${DNAME},DNS:elasticsearch-${DNAME}-0,DNS:elasticsearch-${DNAME}-1,IP:127.0.0.1") -days 120 -in elasticsearch-${DNAME}.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out elasticsearch-${DNAME}.crt -sha256






