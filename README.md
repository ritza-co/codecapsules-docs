# Code Capsules Documentation Site
TODO: Welcome...

# Setup

TODO: Insert how to setup on a fresh machine, like mac and windows (assume no python knowledge)

# Dev

* Clone the build repository at https://github.com/codecapsules-io/mkdocs-material-insiders
* Build the docker container for that repository locally with `docker build . -t cc-docs-env`
* Change directory into this repository and serve the docs with `docker run --rm -it -p 8000:8000 -v ${PWD}:/docs cc-docs-env`
* You can now visit http://localhost:8000 in your browser and see a live preview of the markdown

# Running

docker run -p 5555:5555 -d eu.gcr.io/appstrax/code-capsules-docs:1.0.41

# Building and Deploying

docker build -t eu.gcr.io/appstrax/code-capsules-docs:1.0.41 .

docker push eu.gcr.io/appstrax/code-capsules-docs:1.0.41
