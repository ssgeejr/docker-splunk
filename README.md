# docker-splunk
Logging with dockers built in splunk logger

First establish your credentials, you will need two items for HEC logging

* HEC_TOKEN
* HEC_URL 

For universal logging, put them under /etc/docker/daemon.json 

```
{
"log-driver": "splunk",
        "log-opts": {
                "splunk-token": "4S4SGJR9-8D74-HE33-KI12-JH2J1HDK39AS",
                "splunk-url": "https://hec.yourdomain.com:8088",
                "splunk-verify-connection": "true",
                "splunk-index": "tomcat",
                "splunk-format": "json",
                "splunk-sourcetype": "docker:tomcat"
        }
}
```

In your docker-compose: 

```
version: "3.7"

services:
  tomcat:
    image: tomcat:8
    container_name: tomcat
    logging:
        driver: splunk
        options:
            splunk-format: "json"
            splunk-token: "4S4SGJR9-8D74-HE33-KI12-JH2J1HDK39AS"
            splunk-url: "https://hec.yourdomain.com:8088"
            splunk-index: "tomcat"
            label: "tomcat_splnk"
            tag: "{{.ImageName}}/{{.Name}}/{{.ID}}"
    ports:
      - 8000:8080
```

Stand-alone docker run 
```
#!/bin/bash

docker run \
    --name tomcat \
    --rm \
    -ti \
    -p 8080:8080 \
    --log-driver=splunk \
    --log-opt splunk-token=4S4SGJR9-8D74-HE33-KI12-JH2J1HDK39AS \
    --log-opt splunk-url=https://hec.yourdomain.com:8088 \
    --log-opt splunk-index=tomcat \
    --log-opt tag="{{.Name}}/{{.FullID}}" \
    --log-opt labels=tomcat_splnk \
    --log-opt env=DEV \
    tomcat:8
```

If you nee to validate your connection using curl 

```
curl -k "https://hec.yourdomain.com:8088" \
 -H "Authorization: Splunk 4S4SGJR9-8D74-HE33-KI12-JH2J1HDK39AS" \
 -d '{"sourcetype": "manual", "event": "Hello, world!"}
```


