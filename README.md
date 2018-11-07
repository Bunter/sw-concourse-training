# Concourse Training Labs
This training session built to be taken after our basic BOSH course.  The goal of the training is to deploy and
upgrade the Hello World bosh release that is deployed in that session.

---
---
## Prerequisites
  * `fly` cli is installed
  * `bosh` cli is installed
  * Familiarity with BOSH enough to understand a basic BOSH release
  * Access to a lab BOSH director and all the necessary credentials
  * A Github Account and your Github user name
  
---  
---
## Lab 1: Setup your Environment

### How to setup this lab

* Fork this repository in to your own github account
* Clone your fork of this repository locally
```bash
git clone git@github.com:$GITHUB_USERNAME/sw-concourse-training.git
```

* Edit the `nginx_release/ci/settings.yml` file
These settings will be interpolated in to other files throughout the tuotorial and save you some typing
This is also a common pattern used in managing more complicated pipelines

* rename `set_env.sh.example` to set_env.sh and fill in the information it asks for and then source the file
```bash
  #These variables are provided by your instructor
  export CONCOURSE_TEAM_NAME=
  export CONCOURSE_PIPELINE_URL=
  export GITHUB_USERNAME=
  export CONCOURSE_USERNAME=
  export CONCOURSE_PASSWORD=
```
* Source the file in to your shell.
```bash
source set_env.sh 
```


### Create your release
  ```bash
  cd nginx_release
  mkdir releases
  bosh create-release --force --tarball releases/release.gz --timestamp-version 
  ```
  You should now have a BOSH Release in the releases of this project

### Commit your release and push it back up to your Github fork
```bash
git commit -am 'updated release for deployment'
git push
```

### Installing the `fly` Command
Visit <CONCOURE_URL> and get the URL from the lower right

### Targeting your concourse Pipeline
```
fly --target concourse-tutorial   login --concourse-url $CONCOURSE_PIPELINE_URL -k  -n $CONCOURSE_TEAM_NAME  -u $CONCOURSE_USERNAME -p $CONCOURSE_PASSWORD
```

Have a look at `ci/lab1.yml`, and we'll merge those changes in to your pipeline
```bash 
spruce merge ci/settings.yml ci/lab1.yml > ci/pipeline.yml
```

```bash
fly -t concourse-tutorial set-pipeline -c ci/pipeline.yml -p ${GITHUB_USERNAME}-pipeline
```

### Login to the concourse WEB UI
* Visit the pipeline URL https://<<CONCOURSE_URL>>
* Log In with provided credentials
* Find your pipeline on the Left of the interface
* New pipelines start in a paused state, you can now run your pipeline

---
---
## Lab 2: Building Our first Tasks
* Open `ci/tasks/upload-release.sh`
* Add the following environment variables, provided by the course proctor

```bash
  export BOSH_DEPLOYMENT=<deployment name>
  export BOSH_DIRECTOR=<bosh director url>
  export BOSH_ENVIRONMENT=<bosh director ip>
  export BOSH_CLIENT=<bosh user>
  export BOSH_CLIENT_SECRET=<bosh password>
```
The rest of the task should look familiar if you've been through the BOSH training course
But we need to actually make sure our pipeline knows how to access this release.

### Add the task to the pipeline yml
Have a look at ci/lab2.yml, and we'll merge those changes in to your pipeline
```bash 
spruce merge --prune github --prune release  ci/settings.yml ci/lab2.yml > ci/pipeline.yml
```

### Commit your release and push it back up to your Github fork
```bash
git commit -am 'updated release task'
git push
```

### Update the pipeline
Every time we make a chance to our pipeline we'll need to reset the pipeline
```bash
fly -t concourse-tutorial set-pipeline -c ci/pipeline.yml -p ${GITHUB_USERNAME}-pipeline
```

---
## Lab 3: Add a Deploy Task to the release
* Open ci/tasks/upload-release.sh
* Add the following environment variables, provided by the course proctor

```bash
  export BOSH_DEPLOYMENT=<deployment name>
  export BOSH_DIRECTOR=<bosh director url>
  export BOSH_ENVIRONMENT=<bosh director ip>
  export BOSH_CLIENT=<bosh user>
  export BOSH_CLIENT_SECRET=<bosh password>
```
### Deploy the Release

### Preparing your release for deployment
In the BOSH Training session we learned to create a BOSH release. Here that release has been provided in a
working state.  We'll want to create that release again here and push it back to your fork of this repository.

Have a look at `manifests/nginx-release.yml`, and we'll merge those changes in to your bosh release
```bash 
spruce merge ci/settings.yml manifests/nginx-release.yml > manifests/manifest.yml
```

Have a look at `ci/lab3.yml` and we'll merge those in to your pipeline
```bash
spruce merge --prune github --prune release  ci/settings.yml ci/lab3.yml > ci/pipeline.yml
```

Once again we set the pipeline 
```bash
fly -t concourse-tutorial set-pipeline -c ci/pipeline.yml -p ${GITHUB_USERNAME}-pipeline
```

---
---
### Extra Credit
* Refactor the upload-release task to move the training-bosh.pem file in to a file
or s3 resource
* Update the pipeline to trigger on commit to the BOSH repo
* Point out as many security problems as you can in this release

---
---
## Lab 4: Add a Upload Release Task to the release

---
---
## Lab 5: Using a Credentials file with Concourse

---
---
## Lab 6: Using Vault with Concourse
