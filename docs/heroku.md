### Deploying Retool on Heroku

#### Automatic deploy

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/tryretool/retool-onpremise)

You can deploy to Heroku using the following steps:

1. Click the button above to begin your deployment
1. Provide a unique App name, e.g. `<your-org>-retool`
1. Provide required config vars:
    * `LICENSE_KEY` - your Retool license key
1. Set any optional config vars:
    * `USE_GCM_ENCRYPTION` set to `true` for authenticated encryption of
      secrets; if true, `ENCRYPTION_KEY` must be 24 bytes
1. Click "Deploy app"


#### Manual deploy

You can manually deploy to Heroku using the following steps:

1. Install the Heroku CLI, and login. Documentation for this can be found here: <https://devcenter.heroku.com/articles/getting-started-with-nodejs#set-up>
1. Clone this repo `git clone https://github.com/tryretool/retool-onpremise`
1. Change the working directory to the newly cloned repository `cd ./retool-onpremise`
1. Create a new Heroku app with the stack set to `container` with `heroku create your-app-name --stack=container`
1. Add a database: `heroku addons:create heroku-postgresql:mini`
1. In the `Settings` page of your Heroku app, add the following environment variables:
   1. `NODE_ENV` - set to `production`
   1. `JWT_SECRET` - set to a long secure random string used to sign JSON Web Tokens
   1. `ENCRYPTION_KEY` - a long secure random string used to encrypt database credentials
   1. `USE_GCM_ENCRYPTION` set to `true` for authenticated encryption of secrets; if true, `ENCRYPTION_KEY` must be 24 bytes
   1. `LICENSE_KEY` - your Retool license key
   1. `PGSSLMODE` - set to `require`
1. _Optional_: To select the version of Retool used, just edit the first line
   under `./heroku/Dockerfile` to:
   ```docker
   FROM tryretool/backend:X.Y.Z
   ```
1. Push the code: `git push heroku master`

### Update Heroku deployment

To update a Heroku deployment that was created with the button above, you'll
need to push the desired changes to the Heroku remote.

```zsh
# This assumes you already have the `heroku` CLI installed and are logged in
heroku git:clone --app=<your-retool-app-name> # default remote name is "heroku"
git remote add upstream https://github.com/tryretool/retool-onpremise
# This will incpororate the remote changes into your _local_ copy
# Also assumes both branches are "master"
git pull upstream master
git push heroku master
```