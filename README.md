# docker-mytardis

# Deployment - Docker compose

# Development

## Dev. MyTardis develop branch

```
$ git clone -b develop --recursive https://github.com/UWA-FoS/docker-mytardis.git mytardis
$ cd mytardis
```

Required to contribute the the MyTardis project.

This will pull all the MyTardis develop branch source and set docker-compose.yml file for the latest UWA develop docker image with prerequisites for testing, etc...

To contribute to the MyTardis project please read the [CONTRIBUTING.rst](https://github.com/mytardis/mytardis/blob/master/CONTRIBUTING.rst).

## Dev. UWA production build.

```
$ git clone --recursive https://github.com/UWA-FoS/docker-mytardis.git mytardis
$ cd mytardis
```

Required to add new features and/or settings to the current UWA production MyTardis service.

## General dev. instructions

* rename the relevant env_template.MODULE file removing the "_template" from the name.
* edit the env.MODULE files with the required settings.


* template file that are not required, ensure the files are renamed and blank OR remove the relevant entry in the docker-compose.yml file.
* rename all the env_template.MODULE files by removing the "_template" from the name.
You can do this with a command like the one below. Use echo before mv for a dry run.

```
$ for MODULE in *template*; do mv "$MODULE" "${MODULE/_template/}"; done
```

* edit the resulting env.MODULE files with the required settings.

Settings in Django settings will be automagically pulled into the Django settings in your docker image.

* template files that are not required: ensure the files are renamed and blank OR remove the relevant entry in the docker-compose.yml file.
* edit Dockerfile and/or docker-compose.yml to your desired settings / alterations.

For instance, Django will need an email address to report exceptions to when production code raises them; production code should clearly have debug mode set to "Off" and will email exception reports to the admin rather than display them in the browser, which is obviously a security threat.

For the purposes of development though, it is fine to have debug mode "On" and this shouldn't matter. However, let us change email settings anyway. 

* Let's first run the steps to obtain the latest image from DockerHub. Afterwards we will look at settings changes.


```
$ docker-compose pull                  # acquire the latest image from DockerHub
$ docker-compose up -d                 # start docker containers
$ docker-compose logs --no-color -f    # check logging output

(wait for logging output to stop, usually after a group of lines like this, then interrupt with Ctrl-C:)

        django_1    | [2018-02-26 04:37:36 +0000] [106] [INFO] Booting worker with pid: 106
        django_1    | [2018-02-26 04:37:36 +0000] [111] [INFO] Booting worker with pid: 111
        django_1    | [2018-02-26 04:37:36 +0000] [114] [INFO] Booting worker with pid: 114

$ docker-compose exec django python mytardis.py createsuperuser
```

Once the startup process has completed point you browser to http://localhost:8001/ and login using the credentials provided to the createsuperuser script above.

This development uses [Git Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) to incorporate other code bases into the build process were appropriate, such as the MyTardis source. To work on a different upstream version you should follow "Working on a Project with Submodules", "Pulling in Upstream Changes". E.g.,

```
To pull the latest upstream changes from MyTardis.
From the cloned project directory run
$ cd src/mytardis
$ git fetch
$ git merge origin/master
$ cd ../..
$ docker-compose build
```

* Let's now change the email address, as previously mentioned.

Open docker-compose.yml

Change the DJANGO_ADMINS field. By default, it looks like this- the person whose repository we've forked. Change it to your own name and email address, and save the file.

```
	- DJANGO_ADMINS=[('Dean Taylor','dean.taylor@uwa.edu.au'),]
```

You may also want to change this field:

```
      - DJANGO_DEFAULT_FROM_EMAIL='donotreply-trudat@uwa.edu.au'
```

After editing docker-compose.yml, simply run:

```
$ docker-compose restart
```

* If you are instead changing one of the env.MODULE files, you will need to do:

```
docker-compose build
```
### IMPORTANT: In docker-compose.yml, remember DJANGO_DEBUG should be set to False for production code!!!


# Configuration

Configuration can be accomplished in a number of different was as circumstance dictates.

## Docker build settings

* Dockerfile
* docker-entrypoint.d/

  Processed in the Django containers.

  Dump directory for Docker entrypoint bash scripts.

  The scripts are executed in the startup shell (not spawned) and are processed in lexical order.

* docker-entrypoint_celery.d/

  Processed in the Celery containers.

  Dump directory for Docker entrypoint bash scripts.

  The scripts are executed in the startup shell (not spawned) and are processed in lexical order.

* settings.d/

  Django settings dump directory.

## Docker compose image instantiation settings

* docker-compose.yml
  * env.MODULE

    Entries to these are placed in the docker-compose.yml file [env_file](https://docs.docker.com/compose/environment-variables/#the-env_file-configuration-option) and allow environment settings for container instances/deployments.

    env_template.MODULE templates are provided for examples and reference. Alter the settings for your deployment and rename the files to env.MODULE (remove the _template part of the name).

# References

## Ref. Source

[MyTardis](https://github.com/mytardis/mytardis)

## Ref. Docker

[Django docker container](https://github.com/GoHiTech/docker-django)

