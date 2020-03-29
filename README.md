# Log Cache UI

A GUI for Cloud Foundry [Log Cache](https://github.com/cloudfoundry/log-cache-release/blob/develop/src/README.md).
It's easier than [Log Cache CLI](https://github.com/cloudfoundry/log-cache-cli).

![image](https://user-images.githubusercontent.com/106908/77842862-3be29f80-71d2-11ea-9616-ed0232a256cb.png)
![image](https://user-images.githubusercontent.com/106908/77842871-53218d00-71d2-11ea-963a-aa4a61f76256.png)
![image](https://user-images.githubusercontent.com/106908/77842850-1e153a80-71d2-11ea-913d-0704f43fbfa6.png)

### Create a UAA Client

```
SYSTEM_DOMAN=sys.your-cf.example.com
ADMIN_CLIENT_SECRET=...

uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin -s ${ADMIN_CLIENT_SECRET}

uaac client add log_cache_ui \
  --secret CHANGEME \
  --authorized_grant_types refresh_token,authorization_code \
  --scope openid,doppler.firehose,logs.admin \
  --access_token_validity 43200 \
  --refresh_token_validity 259200 \
  --redirect_uri http://localhost:8080/login/oauth2/code/uaa
```

### Run with Docker

```
docker run \
  --rm \
  -m 768m \
  -e SYSTEM_DOMAIN=${SYSTEM_DOMAIN} \
  -e UAA_CLIENT_SECRET=CHANGEME \
  -p 8080:8080 \
  making/log-cache-ui
```

Go to http://localhost:8080 and login UAA as a user with `doppler.firehose` or `logs.admin` scope like `admin`.

Docker Image is built using [Cloud Native Buildpacks](https://buildpacks.io).
You can run it with less memory using [`openjdk-cnb`](https://github.com/cloudfoundry/openjdk-cnb) options.

```
docker run \
  --rm \
  -m 256m \
  -e SYSTEM_DOMAIN=${SYSTEM_DOMAIN} \
  -e UAA_CLIENT_SECRET=CHANGEME \
  -e JAVA_OPTS="-XX:ReservedCodeCacheSize=32M -Xss512k" \
  -e BPL_THREAD_COUNT=20 \
  -p 8080:8080 \
  making/log-cache-ui
```