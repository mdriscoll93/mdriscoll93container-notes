# Ignoring Files During Build

> In this scenario, we'll explore how you can ignore certain files from ending up inside a Docker image that can introduce security risks. This scenario also investigates how you can reduce the build time by ignoring which files are sent to the Docker Build Context.

## Step 1 - Docker Ignore

To prevent sensitive files or directories from being included by mistake in images, you can add a file named .*dockerignore*.

### Example

A Dockerfile copies the working directory into the Docker Image. As a result, this would include potentially sensitive information such as a passwords file which we'd want to manage outside the image. View the Dockerfile with `cat Dockerfile`

Build the image with `docker build -t password .`

Look at the output using `docker run password ls /app`

This will include the passwords file.

### Ignore File

The following command would include passwords.txt in our .dockerignore file and ensure that it didn't accidentally end up in a container. The .dockerignore file would be stored in source control and share with the team to ensure that everyone is consistent.

`echo passwords.txt >> .dockerignore`

The ignore file supports directories and Regular expressions to define the restrictions, very similar to .gitignore. This file can also be used to improve build times which we'll investigate in the next step.

Build the image, because of the Docker Ignore file it shouldn't include the passwords file.

`docker build -t nopassword .`

Look at the output using `docker run nopassword ls /app`

### Protip

If you need to use the passwords as part of a RUN command then you need to copy, execute and delete the files as part of a single RUN command. Only the final state of the Docker container is persisted inside the image.