
# OpenSearch with  OpenDashboard 

With some tweaks to have it in a sem-production env

## Clone the repo

Use git clone.

## Create the nginx network

Run
`docker network create nginxproxy_net`

## Set env 

Copy the .env.demo

`cp .env.demo .env`

Edit the file with your own settings or leave it as is if you do not intend to use the nginx proxy with let's encrypt.

## Create your own internal_users.yml

Copy the default file

`cp ./config_template/opensearch-security/internal_users.yml`

Edit the file and change the default passwords.

`docker-compose run opensearch-node1 /usr/share/opensearch/plugins/opensearch-security/tools/securityadmin.sh -cd /usr/share/opensearch/plugins/opensearch-security/securityconfig/ -icl -nhnv -cacert /usr/share/opensearch/config/root-ca.pem -cert /usr/share/opensearch/config/kirk.pem -key /usr/share/opensearch/config/kirk-key.pem -t internalusers -f /usr/share/opensearch/config/opensearch-security/internal_users.yml`

Do this for all default users.


## Check docker-compose config

`docker-compose config`

Check and fix reported conf errors. 


## Start Opensearch first

First start opensearch 

`docker-compose up opensearch-node1`

CTRL+C after it finished initialization.


## Start all the services

`docker-compose up -d`

## Check the logs 

`docker-compose logs -f -t --tail=1000`


# Go to browser 

Browse to http://localhost:5601 to login to OpenDashboard


# Public IP: Letsencrypt cert and nginx

Run

`docker-compose -f nginx-compose.yml up -d`

If you have the correct DNS cname/A entries and the correct virtual hosts defined in .env, you will magically get certs from LetsEncrypt. 


# Hash a new password from a oneliner
docker exec -it opensearch-node1 /usr/share/opensearch/plugins/opensearch-security/tools/hash.sh -p YourNewPassword

# Update user passwords

docker exec -it opensearch-node1 /usr/share/opensearch/plugins/opensearch-security/tools/securityadmin.sh -cd /usr/share/opensearch/plugins/opensearch-security/securityconfig/ -icl -nhnv -cacert /usr/share/opensearch/config/root-ca.pem -cert /usr/share/opensearch/config/kirk.pem -key /usr/share/opensearch/config/kirk-key.pem -t internalusers -f /usr/share/opensearch/config/opensearch-security/internal_users.yml