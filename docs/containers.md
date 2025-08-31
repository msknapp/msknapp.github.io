
# Containers

- Docker is the most common.
- Reuses the kernel of the host machine.
- Isolated filesystem, operating system, installed software, runtime.
- Can mount volumes, setup networks, assign environment variables, expose ports.

## Docker

- lightweight, isolated runtime environment.  Includes code, runtime, dependencies, libraries, tools, filesystem, etc.
- running image is called a container.
- images do not include a kernel, they reuse the one from the host.  Operating systems are not needed.  This makes them much lighter.
  and faster to download and boot.
- by having an isolated environment, you can more easily port your application to other host machines.
- virtual machines virtualize the hardware, and include an operating system.  Containers virtualize the operating system.
- docker daemon: builds, runs, and distributes containers (publishing to a registry).  manages images, containers, networks, and volumes.
- docker compose: lets you run a set of containers together.
- images:
    - read-only template for containers.  They are immutable.
    - composed of layers, each one adds a change, like installing an application.
    - can be published to a registry or downloaded from one.
    - are versioned (tagged).
    - when an image is re-built, only layers that have changed get re-run or re-built, and any layers after them.
- containers:
    - can have storage attached, or connect with a network.
    - uses a union filesystem so it sees all files and directories in the image layers.
    - a read-write filesystem layer is added to the top at runtime, so the app can make changes. 
    - can be started, stopped, started again.  Data written in its prior run is still available.
    - if the container is _removed_, then only the changes written to an
      attached persistent storage will be saved.  Anything written to its local read-write volume are deleted.
- if you try running a container for an image that doesn't exist, docker will pull it from a registry, if possible.
- Written in Golang, uses "namespaces" for isolation.
- **registry:** a centralized location that stores container images.
    - registries hold repositories, and repositories hold images.  Images have versions.
    - Most registries also have namespaces that hold the repositories.  These correspond with an organization, team, or group.
    - Dockerhub, AWS elastic container registry (ECR), Google Container Registry (GCR), Artifactory, and Harbor are registries.
    - Dockerhub lets you create public repositories, and one private, for free.
- multi-stage builds: let you build in one stage, and copy the results into another for the final image.  Dependencies that were 
  only needed at build time will not exist in the final image.  This makes it more light weight and also more secure by having fewer
  things installed.
- Writing Dockerfiles:
    - FROM `image`: the base image
    - RUN `commands...`: a command or set of commands
    - EXPOSE `port`: declares that a container uses a port internally, does not actually _publish_ it.
    - WORKDIR `path`: sets the working/current directory.
    - ENV `key value`: sets an environment variable.
    - COPY `from to`: copies a file or set of them from the host to the image.  You can use `--chown` and/or `--chmod` here too.
      Use `--from=` to specify a past build stage to copy from.
      Use `--link=true` to make the layer not be invalidated by changes to previous layers.  This gives you faster builds.
      Using `--link=true` will not work if the destination path contains a symbolic link.  Otherwise, `--link=true` should always be used.
    - ADD `from to`: for most normal files, this acts like copy.  Here are the exceptions:
        - for archives: it will extract them.
        - for git repository URLs: it will git clone them, omitting the `.git` directory unless you pass `--keep-git-dir=true`.
        - for other URLs: it will download them.  You can pass a `--checksume=...` argument for more security.
    - USER `id`: sets the user in future commands and at runtime.
    - CMD `string` or `["cmd", "arg1", ...]`: Sets the default runtime command when the container starts.  Optional.
    - ENTRYPOINT: If set and a container is started with arguments, the arguments are passed to the entrypoint command.
    - ARG `key[=value]`: defines an argument that can be set in the `docker build` command, and optionally sets a default value.
      You can set them at build time by passing `--build-arg key=value`.
    - LABEL `key=value ...`: sets metadata on the image, which can be found by inspection.  You can search for images based on these.
- Boot command:
    - if the `--entrypoint` argument is used with `docker run`: it runs the entrypoint command given on the command line.
      Any extra arguments passed to the container become arguments to the entrypoint command.
    - if no entrypoint is set:
        - if no arguments are provided: it runs all of what was set in the CMD.
        - if arguments are provided: it runs that as _the_ command and its arguments.
    - if an entrypoint is set:
        - if no arguments are provided: it runs all of what was set in the ENTRYPOINT.  If the image also defined a CMD, those
          values become arguments to the entrypoint command.
        - if arguments are provided: it runs the entrypoint defined in the Dockerfile, and passes those arguments as
          arguments to _the_ entrypoint command.
- Understanding Ports:
    - **Publishing:** means that external processes can connect to a container's port.
    - Using the `EXPOSE` command in a Dockerfile does not publish that port.  It's more like documenting that the port is available.
    - Ports can only be published when you run the container, by using -p, -P, or --publish-all.
        - `-p [host-port:]container-port`: lets you publish a port from the container, optionally specifying what port should
          be used on the host as well.  If you don't specify a host port, it chooses an ephemeral one you can discover later.
        - `-P` and `--publish-all` publish all ports that were EXPOSED in the Dockerfile.
    - You can publish ports that were never exposed, and you can expose a port that is never published.
    - It is not necessary to publish or expose a port if it will be connected to by another container in the same network.  It is
      only necessary to publish a port if it must be connected to from outside its own network.
- Runtime Arguments:
    - `-p [host:]internalport` to publish a port.
    - `-v <hostdir|volumename>:<containerdir>[:r{o|w}]` adds a volume _or_ a bind mount.  Can specify read-only or read-write.
      Docker recommends always using `--mount` instead because it has more controls.
    - `--mount type=<type>,{source|src}=<source>,{destination|dst}=<destination>[,options...]` mounts a binding or volume or tmpfs.
        - type can be "volume", "bind", or "tmpfs"
    - `-e key=value` sets an environment variable, or use `--env-file .env` to pass many environment variables from a `.env` file.
    - `-i` interactive
    - `-t` terminal
    - `-d` run as a daemon, in the background
    - `--rm` when it stops, automatically remove the container.
    - `-u` set the user
    - `-w` set the working directory.
- Understanding Volumes:
    - persistent filesystem storage.  Even if a container is stopped and removed, the volume can remain.
    - These are stored under docker's home, they don't map to directories a user provides.
    - volumes get names, you attach them in the docker run command using `-v <volumename>:<directorypath>`
      The left side can either be a named volume, or a path to a directory or file on your local host filesystem.
      If the left side is a valid volume name, that is used, otherwise it's treated as a file path for a bind mount.
    - These can be attached to multiple containers on the host simultaneously, offering a means of sharing data.
    - They usually start empty, like if you `docker volume create <volumename>` it is empty.  
    - If an empty volume is mounted to a directory that already contains files, those files get copied into the volume.
    - If you `docker run -v <name>:<dir>` with a volume name that does not already exist, it will be created automatically.
      Therefore, running this way is also an easy way to pre-populate a new volume.
    - Getting data out of a volume onto the host may require attaching it to a container first.
- Understanding [bind mounts](https://docs.docker.com/engine/storage/bind-mounts/):
    - These attach or mount files/directories from the host machine onto the image.
    - They are ideal for adding configuration, secrets, and/or data inputs from the host.
    - Use volumes if you need filesystem changes to persist even if the container is removed, 
      or if you need to share filesystem data between multiple containers.
      Use bind mounts if you need to pass configuration from the host.
    - Mounting just one file: the path on the host must be absolute, not relative, or it won't work.
- tmpfs mounts will be deleted as soon as the container stops.  Its data won't even exist if the container is restarted.  Use them if
  you need a large file buffer for temporary processing.
- Understanding User and Group IDs:
- Understanding [Multi Platform builds](https://docs.docker.com/build/building/multi-platform/):
    - Enables one image to be built for different operating systems, and CPU architectures (amd64 vs arm64).  Otherwise an emulator is
      needed to run the image, making it slow.  Typically we just worry about linux amd64 and arm64.
    - the docker pull command automatically selects the image for your own platform.
    - docker desktop has a setting you can enable for it to use containerd, which means it supports multi platform builds.  Otherwise you
      need to create a custom builder image using `docker buildx create ... --use`.
    - `docker buildx build --platform linux/amd64,linux/arm64 .`
    - Referring to the platform in the Dockerfile: use [environment variables](https://docs.docker.com/build/building/variables/#multi-platform-build-arguments)
      that are automatically defined in buildx.  Usually `TARGETARCH` is what you want.
- Making better images:
    - Consider a more light-weight base image.
    - Consider separating them into stages so the runtime image has only what is needed.
    - Consider setting a non-root user that has minimal privileges.
    - Consider adding labels to them.
    - Don't copy in configuration at build time, do that at runtime.  It's more maintainable to have images that are unlikely to 
      need changes.
    - Reduce environment variable use.  
    - Put binaries where they already are on the path.
    - Build separate images for debugging that have tools you need.  Use them in non-prod as necessary.  Or add 
      debug tools in a sidecar.
    - expose ports for documentation reasons.
    - Add `--link=true` to copy or add commands.  Avoid using symbolic links.
    - Set a custom prompt PS1.
    - Support different architectures.
    - Automate their build from triggers like git commits.
    - Set a reasonable working directory.
    - Simplify or minimize the application.
    - reduce permissions for containers to modify files on the host or in volumes.
    - minimize the scope of containers, don't have them be responsible for running multiple applications, use multiple containers.
    - for testing, don't include compiled application binaries into images, mount them instead.  Since they change frequently,
      you can save build time this way.

## Kubernetes

- Framework for running and maintaining containers in an operational cluster.
- Pods: one or more containers, does the work.
- ReplicaSets: defines how a set of pods should be run
- Deployments
- StatefulSets
- Service
- Gateway
- ConfigMap
- Secret
- Job
- CronJob
- DaemonSet
- Node
- CustomResourceDefinition
- Admission Controller
- Namespace
- LimitRange
- VirtualService
- Destination
- Helm
- ArgoCD
- Argo Workflows
- PersistentVolume
- PersistentVolumeClaim
- Taints, tolerance
- HorizontalPodAutoscaler

## Istio
