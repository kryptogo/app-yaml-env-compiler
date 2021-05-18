

## Securely storing environment variables in Google App Engine (GAE) with app.yaml


Original repo : ikuanyshbekov/app-yaml-env-compiler

(add a configurable path for the app.yaml)

### The action will help you to solve the following problems:
1. If you need to store API keys or other sensitive information in app.yaml as environment variables for deployment on Google App Engine.
2. If you don't like the idea to push secret environment variables app.yaml to GitHub.
3. If you don't like the idea to store the environment variables in a datastore.




### Action swaps environment variables in app.yaml with the minimal effort

1. Modify the app.yaml file:

        env_variables:
            MY_ENV_VAR1: $MY_ENV_VAR1
            MY_ENV_VAR2: $MY_ENV_VAR2

2. Add this action to your workflow:

        - uses: actions/checkout@v1
        - uses: gaspb/app-yaml-env-compiler@master
          env:
            MY_ENV_VAR1: ${{ secrets.MY_ENV_VAR1 }}
            MY_ENV_VAR2: ${{ secrets.MY_ENV_VAR2 }}
          with:
            appyamlpath: './src/main/appengine/app.yaml' # You can leave this configuration if app.yaml is in the root of your repo

Full example with deployment to Google App Engine:     

    deploy:
        name: Deploy
        runs-on: ubuntu-latest
        needs: [build]
    steps:
        - uses: actions/checkout@v1
        - uses: dovidio/app-yaml-env-compiler@v1.0
          env:
            MY_ENV_VAR1: ${{ secrets.MY_ENV_VAR1 }}
            MY_ENV_VAR2: ${{ secrets.MY_ENV_VAR2 }}
          with:
            appyamlpath: './src/main/appengine/app.yaml'              
        - uses: actions-hub/gcloud@master
          env:
            PROJECT_ID: ${{ secrets.GCLOUD_PROJECT_ID }}
            APPLICATION_CREDENTIALS: ${{ secrets.GCLOUD_AUTH }}
            CLOUDSDK_CORE_DISABLE_PROMPTS: 1
          with:
            args: app deploy app.yaml
