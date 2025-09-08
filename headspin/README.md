# Headspin Personal Knowledge Cloud

# Run Platform Services Locally

- Reference : https://headspin.atlassian.net/wiki/spaces/HTO/pages/974815235/Run+platform+services+locally

# Running UI & API 

## Building UI

- If a local instance of ui is required that depends on the local instance of api, the ui web app will need to be prepared.
- Reference : 
https://github.com/projectxyzio/web/tree/master/ui.headspin.io


Use nvm to install or use the correct Node.js version defined in .nvmrc

```
# at the project root
cd $HEADSPIN_HOME/web/ui.headspin.io

nvm install --latest-npm
# or
nvm use
nvm install-latest-npm
```

To start, install project dependencies

```
npm ci
```


The dev server will serve the UI on http://localhost:9100.

By default, the UI will connect to HeadSpin services (uiend, api and others) on localhost.

```
npm start
npm start -- --env keys=local
In order to connect to services hosted elsewhere, a --env keys=<hsenv> argument may be provided

npm start -- --env keys=canary
```

## Running UI & API

- By default, npm start will launch the web app and attempt to connect to each local service dependency. This requires at a minimum, api and uiend. Launch those first

```
cd $HEADSPIN_HOME/platform
# in one shell instance
python headspin/api/server.py
# in another
python headspin/uiend/server.py
```

Once webpack reports that it has compiled successfully, the web server should be running. Generate a user token to login:

```
hsops auth-token create --email=<email-address>
```

This will return an Auth Link that can be copied into a browser and used to sign in. If all is operating as expected, visiting the login page should cause the local uiend instance to generate logs. Navigate to Remote Control and start a control session on a device. Open the Apps tab in the Remote Control UI and this should cause uiend to issue HTTP requests to api, which should also be reflected with logs in the local api instance. Your local environment is now hosting a basic version of the Headspin platform and all development and debugging can be pursued without any platform deployments.

# Just Running API

 - Changes to api are trivial to test locally. Switch your environment to canary and run the api server.

```
cp $HEADSPIN_HOME/keys-red/canary/pbox_id_rsa $HEADSPIN_HOME/.pbox_id_rsa # this is required to execute remote scripts on a pbox
echo -n local > $HEADSPIN_HOME/.code_env
hsenv canary
cd $HEADSPIN_HOME/platform
python headspin/api/server.py
```

Issue a rudimentary HTTP request to the server to validate that it can receive requests.

```
TOKEN=API_TOKEN_TAKEN_FROM_CANARY_WEB_LOGIN
curl -H "Authorization: Bearer <api-key>" http://localhost:8303/v0/devices/keys
```
