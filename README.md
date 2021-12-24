# docker quick start

For local development:

1. Build / start containers

    docker compose up -d --build

2. Edit /etc/hosts on your workstation, ensuring "golr" resolves to 127.0.0.1:

    127.0.0.1 localhost golr

3. Point your browser to http://localhost:9999

For production deployment:

1. Check out soybase.org/amigo branch

2. Update the public GOLR URLs in conf/amigo.yaml (if necessary):

    AMIGO_PUBLIC_GOLR_BULK_URL:
    ...
    value: http://www.soybase.org/solr/
    AMIGO_PUBLIC_GOLR_URL:
    ...
    value: http://www.soybase.org/solr/

4. Update host ports in compose.yml to not conflict with used ports (if necessary)

5. Deploy on production host by pushing commit to GitHub

# Overview

  This README file will be filled out more in the future. However, for
  the time being, please see the
  [AmiGO 2 Manual](http://wiki.geneontology.org/index.php/AmiGO_2_Manual)
  for more details about the code and installation. You may also be
  interested in the related information found in
  [BBOP JS](https://github.com/berkeleybop/bbop-js).

# Installation

The main installation information is available
[on the wiki](http://wiki.geneontology.org/index.php/AmiGO_2_Manual:_Installation).

# Jenkins (example)

## Loading

This is a load example using
[this configuration](https://github.com/geneontology/amigo/blob/master/conf/examples/amigo.yaml.tomodachi).

```bash
## Get to the app home and make sure we're in the right place.
cd /home/bbop/local/src/git/amigo
git reset --hard && git pull

## Get the environment and variables ready.
npm install
cp conf/examples/amigo.yaml.tomodachi conf/amigo.yaml
## Need to generate config.pl
./node_modules/.bin/gulp install

## Do the load.
./node_modules/.bin/gulp message-load-start
/bin/rm -f /tmp/golr_timestamp.log
./node_modules/.bin/gulp golr-purge
./node_modules/.bin/gulp check-ontology-data
./node_modules/.bin/gulp load-all
./node_modules/.bin/gulp message-load-clear
```

## Deployment & Unit Tests

This is an example automatic deployment and unit/app testing using
[this configuration](https://github.com/geneontology/amigo/blob/master/conf/examples/amigo.yaml.tomodachi).

```bash
## I believe Xvfb should die on shell exit?
Xvfb :1 -screen 5 1024x768x8 &

## Get to the app home and make sure we're in the right place.
cd /home/bbop/local/src/git/amigo
git pull && git reset --hard

## Get the environment and variables ready.
npm install
cp conf/examples/amigo.yaml.tomodachi conf/amigo.yaml
## Need to generate config.pl
./node_modules/.bin/gulp install

## Make the proper python virtualenv setup with the libs we need.
cd test-app/behave
virtualenv `pwd`
source bin/activate
pip install selenium behave jsonpath-rw
## Get back to top.
cd ../..

## Run unit tests against AmiGO labs (master).
DISPLAY=:1.5 ./node_modules/.bin/gulp tests

## Pop out of our wonderland.
deactivate
```

# Releases

The SOP for releases is:

* `npm install`
* `gulp release`
* `cd javascript/npm/amigo2-instance-data/ && gulp release && cd ../bbop-widget-set && gulp release && cd ../../..`
* `git commit -a -m "SOP update for metadata"`
* `git push`
