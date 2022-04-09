# SpringBoot-Cloud-Kafka-SSL

### create certs command

    rm -rf secrets
    mkdir secrets
    echo "datahub" > secrets/cert_creds

    keytool -genkey -noprompt \
         -alias broker \
         -dname "CN=localhost, OU=test, O=datahub, L=paris, C=fr" \
         -keystore secrets/broker.keystore.jks \
         -keyalg RSA \
         -storepass datahub \
         -storetype JKS \
         -keypass datahub  >/dev/null 2>&1

    keytool -keystore secrets/broker.keystore.jks -alias broker -certreq -file secrets/broker.csr -storepass datahub -keypass datahub >/dev/null 2>&1

    openssl req -new -x509 -keyout secrets/datahub-ca.key -out secrets/datahub-ca.crt -days 365 -subj '/CN=localhost/OU=test/O=datahub/L=paris/C=fr' -passin pass:datahub -passout pass:datahub >/dev/null 2>&1

    openssl x509 -req -CA secrets/datahub-ca.crt -CAkey secrets/datahub-ca.key -in secrets/broker.csr -out secrets/broker-ca-signed.crt -days 365 -CAcreateserial -passin pass:datahub  >/dev/null 2>&1

    keytool -keystore secrets/broker.keystore.jks -alias CARoot -import -noprompt -file secrets/datahub-ca.crt -storepass datahub -keypass datahub >/dev/null 2>&1

    keytool -keystore secrets/broker.keystore.jks -alias broker -import -file secrets/broker-ca-signed.crt -storepass datahub -keypass datahub >/dev/null 2>&1

    keytool -keystore secrets/broker.truststore.jks -storetype JKS -alias CARoot -import -noprompt -file secrets/datahub-ca.crt -storepass datahub -keypass datahub >/dev/null 2>&1

### Reference

- https://github.com/Pierrotws/kafka-ssl-compose