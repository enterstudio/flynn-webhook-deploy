Flynn GitHub Webhook Deploy
===========================

[![Circle CI](https://circleci.com/gh/lmars/flynn-webhook-deploy.svg?style=svg)](https://circleci.com/gh/lmars/flynn-webhook-deploy)

Deploy GitHub repositories to Flynn on git push.

Usage
-----

Generate a secret token to include in the GitHub webhook (see [here](https://developer.github.com/webhooks/securing/)
for more information):

```
$ openssl rand -hex 20
1d7f852c9e3f8ef889a42dfc823f57c2452df5bb
```

Deploy this Go app to Flynn with the secret token and a Postgres DB:

```
$ git clone https://github.com/lmars/flynn-webhook-deploy.git
$ cd flynn-webhook-deploy
$ flynn create webhook-deploy
$ flynn env set SECRET_TOKEN=1d7f852c9e3f8ef889a42dfc823f57c2452df5bb
$ flynn resource add postgres
$ git push flynn master
$ flynn scale web=1
```

You can now add repos by going to `https://webhook-deploy.$CLUSTER_DOMAIN` in
your browser, or using psql from the command line:

```
flynn pg psql -- -c "INSERT INTO repos (name, app) VALUES ('lmars/go-flynn-example', 'go-app')"
```

With a repo added, this app will now accept GitHub push events to
`https://webhook-deploy.$CLUSTER_DOMAIN` for repos in the db and deploy them
to the corresponding app (e.g. in the example above, push events for
`lmars/go-flynn-example` will be deployed to the `go-app` Flynn app).
