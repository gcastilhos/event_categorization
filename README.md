# events_hash

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

## Environment Variables

To set any environment variable to be read by the Vue app use
```
VUE_APP_<name>=<value>
```

> Example:  
  VUE_APP_DELAY=10000 # Setting a 10-second delay between requests

This variable must be in a `.env.*` file. The files are specific for each environment

* .env.production
* .env.staging
* .env.local

Along with that, also the `NODE_ENV` variable should be set in both staging and production environments.

On `Heroku`
```
heroku config:set NODE_ENV=development --app eventcategorization-dev # if not set, assumed as development
heroku config:set NODE_ENV=staging --app eventcategorization-staging
heroku config:set NODE_ENV=production --app eventcategorization
```

### The static.json File

For static applications, a `static.json` file must be added, following the format
```
{
  "root": "dist",
  "clean_urls": true,
  "routes": {
    "/**": "index.html"
  }
}
```

Also, ensure that these buildpacks are installed (check the [deployment page][1] for more details)

```
heroku buildpacks:add heroku/nodejs
heroku buildpacks:add https://github.com/heroku/heroku-buildpack-static
```

[1]: https://cli.vuejs.org/guide/deployment.html#heroku
