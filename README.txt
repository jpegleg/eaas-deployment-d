# eaas-deployment-d

Built for Ubuntu 20 microk8s + docker deployment templating.

Clone the git repo and then build out the files/changes you need.
If you actually use this on your own, I expect that you might:
- update eanginx to include an actual NGINX configuration and web content
- replace all instances of ACCOUNT with the user or customer id for the deployment
- update /srv/persist/ACCOUNT/ file structures to contain the backend app and other files required
- edit the eaasapi/prod.cfg to include URI context matching and events that trigger apps/backends

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

Note this install method removes SSH access to the Ubuntu host. 
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
