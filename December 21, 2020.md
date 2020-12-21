# Optimising Dockerfile with OnBuild

> In this scenario, we'll look at how you can optimise Dockerfile using the OnBuild instruction.The environment has been configured with an example Node.js application however the approaches can be applied to any image. The machine name Docker is running on is called docker. If you want to access any of the services, then use docker instead of localhost or 0.0.0.0.

## Step 1 - OnBuild

While Dockerfile's are executed in order from top to bottom, you can trigger an instruction to be executed at a later time when the image is used as the base for another image.

The result is you can delay your execution to be dependent on the application which you're building, for example the application's package.json file.

Below is the Node.js OnBuild Dockerfile. Unlike in our previous scenario the application specify commands have been prefixed with ONBUILD.

```Docker
FROM node:7
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
ONBUILD COPY package.json /usr/src/app/
ONBUILD RUN npm install
ONBUILD COPY . /usr/src/app
CMD [ "npm", "start" ]
```

The result is that we can build this image but the application specific commands won't be executed until the built image is used as a base image. They'll then be executed as part of the base image's build.

We'll see how this base image looks in the next step.