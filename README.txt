# eaas-deployment-d

Built for Ubuntu (20) microk8s + docker deployment templating.

The microservices stack in this template is:
 alphine SFTP site
 HAProxy TLS gateway
 NGINX frontend/web
 Simple Event Correlator frontend/event controller (for an API backend etc, not included here)

...

Clone the git repo and then build out the files/changes you need.

If you use this on your own, I expect that you might:
- update eanginx to include an actual NGINX configuration and web content
- replace all instances of ACCOUNT with the user or customer id for the deployment
- update /srv/persist/ACCOUNT/ file structures to contain the backend app and other files required
- edit the eaasapi/prod.cfg to include URI context matching and events that trigger apps/backends
- update the sftp user "transfer" name and password in eatransfer/users.conf

Example tree of the persistent volumes in /srv/persist/
ACCOUNT
├── bin
│   └── ACCOUNT-api # put your apps/backends here
├── gpg
│   └── .gnupg 
│        ├── private-keys-v1.d
│        ├── pubring.kbx
│        ├── random_seed
│        ├── S.gpg-agent
│        ├── S.gpg-agent.browser
│        ├── S.gpg-agent.extra
│        ├── S.gpg-agent.ssh
│        └── trustdb.gpg
├── log # the eanginx (NGINX) logs
│   ├── access.log
│   └── error.log
├── var # free for backend/app workspace 
│   └── tmp
└── www
    └── transfer # output from backend/s here to be pulled by sftp
        ├── 20200616061639983916590-id.priv.pem.asc
        ├── 20200616061639983916590-id.pub.pem
        ├── 20200616085020773048762-id.priv.pem.asc
        └── 20200616085020773048762-id.pub.pem



Build steps:
Get yourself a Ubuntu 20 install with microk8s and docker in the PATH
chmod +x install
./install

Note this install method removes SSH access to the (Ubuntu) host. 
The point of this type of deployment is to create "an appliance" that will
run on its own for the most part and can work on small scale including IoT.
It takes less than 2 GB of RAM to run a set of these default microservices.


Edit the install script to include this if you want to be able to ssh to the underlying host etc:

ufw allow 22/tcp
ufw reload

Another note, if you end up manually creating deployments for some reason:
The eaproxy-deployment-d relies on some edits to happen in the eaproxy docker build
to populate the proxy backend targets, in this case the NGINX ClusterIP.


Don't hestitate to reach out.

carefuldata@protonmail.com

https://carefuldata.com/



...


Design choices

- Ubuntu (20) with snap and microk8s for easy of deployment and kernel version, inline with upstream development.
- UFW to firewall away almost everything by default, including the kubernetes api etc, leaving only the container services for HAProxy and SFTP exposed via NodePorts.
- The local docker registry is kept out of micok8s and is used purely as a build tool.
- An instance of deployment d takes ~800 MB of RAM and can scale horizontally very well.
- HAProxy as the first layer to the API for world class performance and security.
- NGINX as the web server for configuration flexibility and resilience.
- Simple Event Correlator for event management because of light footprint, rate/suppression controls, ease for onboarding and quick changes, allows arbitrary backends to work together in harmony, can be used for security alerting etc, reliability and performance. Much like an application firewall in this usage.
- Local persistant volumes can be replicated, network mounted, or switched out with other volume services.
- HAProxy service is full ephemeral with no persistance while the others are mounted for ease of management.
- API requests are going to result in HTTP 404s from NGINX by default, just about everything will 404 unless you put something there, but that doesn't stop the backend from running successfully as long as the URI context in the prod.cfg matches. You can of course make the pattern matching include requiring a 200 OK if you want. But there is obfuscation power in just letting everything 404 if you can. Only the user with an sftp account etc will know if the request was successful.

I like to update the matching to require a POST and somewhat-secret URI context, along with other layers authentication/identity.
And then allow the URI context data to be read by the backend if parsed as a valid event:

curl -X POST https://my-eaas-api-service.com/accountperson/api/v2/fullreg1/ec/host/cf83e1357eefb8bdf1542850d66d8007d620e405

In the example above, that last cf83e1357eefb8bdf1542850d66d8007d620e405 part is encoded data that is submitted into the backend.
The user or system who does the curl there will not see any results per se, then systems/they can pull the results via sftp as required. This creates a separation of roles in the API usage, allowing applications and automation to immediately move on from the API call without having to wait around for a response or job completion. Then the data in cf83e1357eefb8bdf1542850d66d8007d620e405 is perhaps further transformed (encrypted/formated) and stored on the SFTP site unless people or automation collect it and remove it, or whatever you want your backend to do. If you would like a backend for you or your business but don't know how to go about it, please contact carefuldata@protonmail.com

# connect into an interactive sftp session:
sftp -oPort=30022 transfer@my-eaas-api-service.com:/portal/transfer/

# or pull a file you know about
sftp -oPort=30022 transfer@my-eaas-api-service.com:/portal/transfer/transfer.static.bin .

Ideally set up with ssh keys for client authentication when applicable.

