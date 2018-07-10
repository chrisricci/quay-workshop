# Quay Workshop

**Prerequisites**
1. An OpenShift cluster accessible or a local cluster using Minishift or oc cluster up
2. Docker CLI installed
3. Create an account on quay.io
4. Create a fork of https://github.com/cricci82/hello-world-instrumented

**Create a private repository** 
1. Git clone the hello-world-instrumented git repository
2. Log into quay.io with docker cli
```
$ docker login quay.io
```
3. Build the hello-world-instrumented container and push to your quay.io repository
```
$ docker build -t quay.io/<org_name>/hello-world-instrumented:v1 .
$ docker push quay.io/<org_name>/hello-world-instrumented:v1
```
4. From quay.io navigate to the newly created hello-world-instrumented repository.  Notice it is Private by default

**Configure Build Trigger**
1. Navigate to Build section of the repository and Create Build Trigger
2. Select your GitHub organization and hello-world-instrumented repository
3. Choose the strategy for triggering builds.  Optionally, filter on a specific branch or tag
4. Optionally, if the base image in the Dockerfile is private you must specify a robot account that has access to pull this image.
5. Commit a code change and verify that a build is triggered

**Configure Pull Secret for OpenShift**
1. Navigate to the settings for the hello-world-instrumented repository in Quay.io and create a robot account with read permissions.
2. Click on the newly created robot account.  Select Kubernetes Secret and download the yaml file
3. From the CLI, create the secret from the yaml file and link to the default service account in the OpenShift Project
```
$ oc new-project hello-world-instrumented
$ oc create -f path/to/downloaded/secret.yaml
$ oc secrets link default <name of secret> --for=pull
```
4. Deploy the container and verify it successfully pulls
```
$ oc new-app quay.io/<org_name>/hello-world-instrumented:v1
```
**Explore the vulnerability scan results**
1. From the Tags section of the repository, click on the security scan results
2. Back on the repository settings, create a notification for when vulnerabilities are discovered
3. Select Medium vulnerability and choose email as the notification method.  Verify the selected email address if required.
4. Change the base image in the Dockerfile to `python:3.7-rc` and commit the change to trigger a new build.
5. The new build should contain several high vulnerabilities.  Verify that the email is triggered once the scan completes

**Explore Time Machine**
1. Navigate to the Tags section of the repository
2. Select one of the tags and delete it 
3. Navigate to Tag History to view the changes to image tags
4. On the delete event, click on the link to restore the tag
5. Navigate back to the Tags section and observe that the tag has been restored
