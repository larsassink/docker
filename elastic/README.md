# Elasticsearch

Awesome but heavy

## Token
You need a token to connect Kibana to Elastic. 

### 1.
First start elastic with: `docker compose up -d elastic`


### 2.
``` bash
docker exec -it elastic-elasticsearch-1 bin/elasticsearch-service-tokens create elastic/kibana kibana-new-token
```

### 3.
Now place the token in the `kibana.yml` file: `elasticsearch.serviceAccountToken:xxxxxxxx`
Also generate a32 char string:

``` bash
xpack.security.encryptionKey: "32_CHAR_HASH"
xpack.encryptedSavedObjects.encryptionKey: "32_CHAR_HASH"
xpack.reporting.encryptionKey: "32_CHAR_HASH"
```

### 4.
Run kibana with: `docker compose up -d kibana`
