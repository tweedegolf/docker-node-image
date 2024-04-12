# Node.js Docker images
This image contains Node.js, yarn and a postgresql client and basic build
tools. For yarn we use the 1.0 version distributed from their apt repository.
Currently these tags are available:

* Node.js 18: `18`
* Node.js 20: `20`, `lts`, `latest`

All versions contain node.js at the specified version, npm and yarn. They also
include the postgresql client applications (e.g. psql and others) and include
basic build tools allowing you to build C/C++ node.js extensions as well.

## Extended images
You can also use the extended images (with the postfix `-extended`). These
variants include dependencies for running browsers such as for usage with
puppeteer.

## Full images
A larger variant still is also available (with the postfix `-full`). These
images include preinstalled firefox and chromium browsers, making them suitable
for end-to-end testing.

## Usage
For basic usage instructions, also see our [debian image] detailed usage
instructions. Basic usage when using docker compose is shown below:

```yaml
services:
    # ...
    app:
        image: ghcr.io/tweedegolf/node:lts
        user: "$USER_ID:$GROUP_ID"
        command: [npm, run, server]
        volumes: [".:/app"]
        working_dir: /app
    # ...
```

## Extending for production
When running this image in a production setting, you should set up a few things:

* Create a user that your application will run under
* Make sure that user will be the default user
* Set the `ROOT_SWITCH_USER` environment variable to your user as well to
  prevent the application accidentally running as root
* Copy the full appllication and its dependencies into the image
* Set a command that will run the application

See for example the Dockerfile below:

```Dockerfile
FROM ghcr.io/tweedegolf/node:lts
RUN useradd -C Application -m -U app
ENV ROOT_SWITCH_USER app
ENV NODE_ENV production
COPY --chown=app:app ./build /opt/application
WORKDIR /opt/application
CMD ["node", "index.js"]
USER app
```

[debian image]: https://github.com/tweedegolf/docker-debian-image
