notes-router
============

mind dump for writing a node process manager that allows you to deploy multiple versions of an application and route to specific versions based on flipr configuration

# Goals
* Deploy application without it receiving any traffic
* All versions of application accessible from a single port
* All versions of application run in a separate process
* Router process provides api/cli to query information on deployed/active versions and execute commands
  * What versions are active
  * Last hit timestamp
  * workers per version?  need lb
  * Commands to stop/start/restart version + workers, increase/decrease workers
* Router uses flipr and etcd to consume rules for routing to specific versions
  * Concerns about slowdown

# Questions
## How do we handle different types of node applications (restify, express, bus subscriber)?
The router must know how to route a request to the application.  For restify/express, we should be able to forward the request to the application.  Tricky for a bus subscriber...the router would need to be able to intercept the message and pass it on to the app process.  Not sure how to pull that one off yet.

# Thoughts
* Router watches a directory for new folders.  The folder name is the git hash, contains the deployed app.  When a new version is detected, it forks a process for the app, figures out port collisions, and creates a mapping between the version and the port so it knows how to forward requests.
* Apps will have to accept a port argument to listen on.
* Router will have to handle port collisions and pass open port to deployed version
* Use [node-http-proxy](https://github.com/nodejitsu/node-http-proxy)?  This should allow us to forward all http requests, targeting specific ports.
* http://substack.net/bounce_http_requests_with_bouncy  
* Monitor stdout of apps to catch death errors
* One master process per service, or one master process for all services on box?
* Is there a stronng need for running multiple versions?
  * DEV: Yes, but not if we use local vagrant environments.
  * TEST: If we followed CI + feature flags, no.  If we don't, then yes.
  * PROD: No, if we want to release features incrementally, we should use feature flags.

Entry point
  api
  cli
  maintains state
    active versions
    traffic stats
  monitors folder

# CI deployment pipeline...
create branch
do small work using feature flags
use vagrant environment for all dev testing
when satisfied, create PR
get some +1s
merge to master
ci server picks it up, deploys to test.
qa signs off
one click deploy to prod
one click rollback if issues

this eliminates the need to run multiple versions of an app, because you are no longer deploying feature branches.

issues:  if something gets merged to master that isn't safe to deploy (dependency upgrade?)
