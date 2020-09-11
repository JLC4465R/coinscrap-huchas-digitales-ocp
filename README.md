# coinscrap-huchas-digitales-ocp

#### Create the Application 'webapp'
```
oc process -f build_coinscrap_webapp_cents_template.yaml -p APPLICATION_NAME=webapp \
-p SOURCE_REPOSITORY_URL=https://github.com/arq-dom-san-digital/coinscrap-huchas-digitales-ocp.git \
-p CONTEXT_DIR='webapp/1.0' -p APPLICATION_PORT=3000 -p SOURCE_SECRET=github-user \
-p APPLICATION_TAG=v1.0 -p SOURCE_REPOSITORY_REF=master | oc apply -f-
```