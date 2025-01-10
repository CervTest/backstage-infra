# Backstage Infra Repo

Infra repo for Backstage (k8s etc). Goes with the associated app repo which produces a container with Backstage in it, ready for this to deploy.

## GAR image publishing

See https://github.com/MovingBlocks/Logistics/tree/repatriation/jenkins#setting-up-google-artifact-repository for initial setup to where a credential is ready in Jenkins. Files here have been updated to point to the new home.

## Secrets

The following must be added as credentials to a Jenkins running a pipeline for this repo. The deploy will take the credentials from Jenkins, paste them into `backstage-secrets.yaml` only locally, then apply those to Kubernetes as secrets that will in turn be available to Backstage.

* (Secret text) id `backstage-backend-secret` with description "A secret for backend usage within Backstage" and any arbitrary gibberish that could make up a key for use one day
* (Secret text) id `backstage-postgres-password` with description "Password used for the Backstage Postgres DB (in three different actual user/pass combos)" and whichever password is desired (not important or used manually, but important to set explicitly)
* (Secret text) id `backstage-github-token` with description "GitHub token for basic Backstage integration (like ability to create repos under the CervTest org)" and a classic or fine grained GitHub token with the right sort of access
* (Secret text) id `backstage-github-auth-client-id` with description "Client id for an OAuth app allowing login for Backstage" and the client id for that app
* (Secret text) id `backstage-github-auth-client-secret	` with description "Client secret for an OAuth app allowing login for Backstage" and the secret
* (Secret text) id `premium-backstage-plugins-spotify-license` with description "The license key for the Spotify premium Backstage plugin bundle" and the license key text from Spotify as a value

Additionally a secret for Google Artifact Registry must be available (id `jenkins-gar-sa`), this is covered in the separate Logistics repo.