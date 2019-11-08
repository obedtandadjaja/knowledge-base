# Deployments

## Continuous deployment

## Blue-Green deployment

Technique that reduces downtime and risk by running two identical production environments called `Blue` and `Green`. At any time, only one of the environments is live, with the live environment serving all production traffic.

As you prepare a new version of software, deployment and the final stage of testing takes place in the environment that is not live. Once it is deployed and fully tested, you switch the router so all incoming requests will go to new environment.

Note: If your app uses relational database, blue-green deployment can lead to discrepancies between your Green and Blue databases during an update. To maximize data integrity, configure a single database for backward and forward compatibility.

Resource: https://docs.cloudfoundry.org/devguide/deploy-apps/blue-green.html

## Canary deployment

Technique that reduces risk by running a version of the newly updated application on a node in the production environment. Once it has been running for some time and no errors or issues are raised then the update can be rolled out to other nodes. In this way, any errors or issues introduced by the update will not affect 100% of users right away.
