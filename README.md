# Salade2chats/Angular

Image with Node, Yarn and AngularCLI

**Versions:**

* Node v10.9.0
* Yarn 1.10.1
* AngularCLI 6.2.3

Including:

* Alpine 3.8
* GoSU 1.10

Default command is 'bash'.

#### Run the image with all capacities arguments

```Shell
$ sudo docker run --rm -ti --name angular \
    --user $(id -u):$(id -g) \
    --volume $(pwd):/data \
    --expose 4200
    --publish 4200:4200
    salade2chats/angular-cli:1.6.8 \
    [command]
```

##### Arguments detail

* `--user $(id -u):$(id -g)`
Needed to be a non-root user, and keep host right owner. A /home/visitor folder is automatically created.
* `--volume $(pwd):/data`
Mount the current folder into /data (default folder)
* `--expose 4200`
Expose default Angular serve port
* `--publish 4200:4200`
Map exposed port on host

#### How to view served application on host

Start Angular serve with `--host 0.0.0.0` property

Example: `yarn run start --host 0.0.0.0`

#### Docker hub

[salade2chats/angular](https://hub.docker.com/r/salade2chats/angular/)
