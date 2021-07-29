# Multi Machine automated deployment

## Sparta Node Sample App

## Description

This app is intended for use with the Sparta Global Devops Stream as a sample app. You can clone the repo and use it as is but no changes will be accepted on this branch. 

To use the repo within your course you should fork it.

The app is a node app with three pages.
- trigger webhook
### Homepage

``localhost:3000``

Displays a simple homepage displaying a Sparta logo and message. This page should return a 200 response.

### Blog

``localhost:3000/posts``

This page displays a logo and 100 randomly generated blog posts. The posts are generated during the seeding step.

This page and the seeding is only accessible when a database is available and the DB_HOST environment variable has been set with it's location.

### A fibonacci number generator

``localhost:3000/fibonacci/{index}``

This page has be implemented poorly on purpose to produce a slow running function. This can be used for performance testing and crash recovery testing.

The higher the fibonacci number requested the longer the request will take. A very large number can crash or block the process.


### Hackable code

``localhost:3000/hack/{code}``

There is a commented route that opens a serious security vulnerability. This should only be enabled when looking at user security and then disabled immediately afterwards

## Usage

Clone the app

```
npm install
npm start
```

You can then access the app on port 3000 at one of the urls given above.

## Tests

There is a basic test framework available that uses the Mocha/Chai framework

```
npm test
```

The test for posts will fail ( as expected ) if the database has not been correctly setup.


## Jenkins Pipeline

<p align=center>
	<img src=jenkins_diagram.PNG>
</p>

### Git Create new key for Jenkins

- Go into your localhost .ssh folder `cd ~/.ssh`
- Create key `ssh-keygen -t ed25519 -C "fsilva@spartaglobal.com"`
	- file to save: `/C/Users/pabfi/.ssh/filipejenkins`
	- password: leave blank
- Check the key `cat filipejenkins.pub`
- Copy it and paste on GitHub repo, under repo settings, deploy keys, add key

### Jenkins Create Simple Pipeline

#### Create a pipeline item to be ran when the other item runs

1. Connect to given IP and login with correct details
2. Create a new item `new item` and give it a name `eng89_filipe`
3. Choose `freestyle_project`
4. Press OK
5. Give a description to the project (Optional)
6. Discard old build
7. max # of build to keep `3`
8. add build steps
9. execute shell `uname -a`
10. apply and save

#### Create another item

1. Do steps 2-9 from above with name `eng89_filipe_pipeline`
2. post build actions
3. Build other projects
4. name it the pipeline item 
5. Trigger only if build is stable
6. apply and save

- Build the main job and see if it ran, also check if it built a new job for pipeline item
- To see if job ran, check console output next to each item name


### Jenkins Pipeline with GitHub

1. New item `filipe_cicd_pipeline`
2. Freestyle project
3. Click on GitHub project and copy the URL from `code > https` and paste on Jenkins
4. Restrict where this project can be run `sparta-ubuntu-node`
5. Source Code Management > Git > go to GitHub and copy URL from `code > SSH` and paste it on repository url in Jenkins
6. Credentials add the private key > add > Jenkins:
	- Kind `SSH username with private key`
	- username `eng89_filipe`
	- Private Key enter directly > Paste your private key from localhost INCLUDING THE `------BEGIN OPENSSH AND -----END ---`
7. Select the new key on Credentials
8. Branches to build - change to */main
9. Check `GitHub hook trigger for GITScm polling`
10. Check `Provide Node & npm bin/ folder to PATH`
11. Build execute shell
	- cd app
	- npm install
	- npm test
12. Save and Build

- Go to job > workspace and check that Jenkins cloned the git repo

#### Create a web-hook for Jenkins

- Copy Jenkins IP address from URL
- Go to GitHub > Repo Settings > webhooks > add webhook > Payload URL `http://[JENKINSIP:PORT]/github-webhook/`