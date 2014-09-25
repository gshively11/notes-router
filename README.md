notes-router
============

mind dump for writing a node process manager that allows you to deploy multiple versions of an application and route to specific versions based on flipr configuration

# Goals
* Deploy application without it receiving any traffic
* All versions of applicaiton accessible from a single port
* All versions of application run in a separate process
* Router process provides api/cli to query information on deployed/active versions
* Router uses flipr and etcd to consume rules for routing to specific versions
* BONUS: Router allows you to spin up multiple instances of a single version and load balances between all of them

# Questions
## How do we handle different types of node applications (restify, express, bus subscriber)?
The router must know how to route a request to the application.  For restify/express, we should be able to forward the request to the application.  Tricky for a bus subscriber...the router would need to be able to intercept the message and pass it on to the app process.  Not sure how to pull that one off yet.

# Thoughts
* Router watches a directory for new folders.  The folder name is the git hash, contains the deployed app.  When a new version is detected, it forks a process for the app, figures out port collisions, and creates a mapping between the version and the port so it knows how to forward requests.
* Apps will have to accept a port argument to listen on.
* Router will have to handle port collisions and pass open port to deployed version
* Use [node-http-proxy](https://github.com/nodejitsu/node-http-proxy)?  This should allow us to forward all http requests, targeting specific ports.