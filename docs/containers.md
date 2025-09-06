
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

Is a set of controllers that continually drive the current state towards a desired state.  IaC workflows run once, K8s controllers run
constantly.
- Architecture:
    - control plane:
        - API: interface for nodes, users, and applications.
        - scheduler: decides where to place workloads.  Assigns pods to nodes.
        - etcd: distributed fault tolerant configuration store.  Consistent and highly available.
        - kube controller manager: monitors and reacts, coordinating changes.  Runs controllers.
        - cloud controller manager: interacts with the cloud provider
    - nodes:
        - kubelet: runs and manages containers assigned to the node.  Uses the k8s api to determine what to  run.
        - kube-proxy: manages network settings and configuration on the node to implement services.
        - container runtime: runs containers.
- labels: key/value, assigned to resources.  Can assign during or after creation.
    - They enable searching for, and filtering of resources.  They are identifying.
    - Limited to 63 characters, alphanumeric plus "." and "-"
- annotations do not enable searching for resources, you cannot search for resources (server side) by annotation.
    - some libraries and tools utilize annotations on resources.
    - Their values are much more permissive.
- namespaces: resource names only need to be unique within their namespace.  Namespaces also enable resource quotas.
- service DNS: The host name format is `<name>.<namespace>.svc.cluster.local`.  If one application is connecting to another service 
  in its own namespace, it doesn't need the full host name, it can simply connect to the service's name.
- pods: 
    - one or more containers that are co-located on one host machine.  They have shared network and storage resources.
    - They support init-containers.
    - Ephemeral in nature.  Assigned to a node.  Never get re-assigned, they get deleted, a new pod is created if needed.
      If a node crashes or is removed, the pods it was running are just deleted.  They don't survive eviction.  They are only ever
      scheduled once in their lifetimes.  Replacements can even use the same name.
    - 
- controllers: manage replication, rollout, and healing.
- Scaling: you can specify multiple pods for a deployment.
- Auto-healing.
  

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
- Argo Workflows
- PersistentVolume
- PersistentVolumeClaim
- Taints, tolerance
- HorizontalPodAutoscaler

Notebook

- if multiple masters, one is leader, doing all operations, others are followers
- etcd is distributed kv store
- master nodes connect to etcd, (not minions)
- scheduler: decides where pods run, knows about resources, constraints, affinity, data locality, QoS, anti-affinity
- controller manager: monitors the state of the cluster, and corrects it if the current state is not the desired state
- pods: collection of one or more containers that are always scheduled together.  It is the "scheduling unit"
- rocket is an alternative container runtime, docker is the default.
- kubelet: accepts master commands over API, executes start/stop of containers/pods
- services: group of pods, an endpoint (DNS) that load balances between the pods.
- kube-proxy: sets up network routes for services, it runs on each worker node.

- etcd: raft consensus, one master, any can be treated as master.  Written in golang.
    - stores cluster state, CMs, Secrets, subnets
- one IP per pod
- Kubernetes uses CNI (CoreOS) to assign IPs, not CNM (docker)
- host OS: have network namespaces
- containers within one pod all share the same network namespace, they can address each other using localhost.
    - this also means the conainers cannot use the same port.
- k8s does not use NAT during pod to pod communication across hosts
- kubeadm: bootstrap k8s cluster, will not provision machines
- kubespray: based on ansible, can install HA k8s clusters on AWS
- containers in a pod share: network namespace, scheduled on same host, same mounted volumes.
- pods don't self heal
- controllers manage replication, fault tolerance, self-healing
- can label any k8s resource for future selection
- label selectors: equality "==", "!=", "="; or set "in", "not in", "exist"
- replication controller: creates/destroys pods until there is a specified number of replicas.  Uses equality based selection
- replica set: also supports set based selectors.
- uncommon to just create replicasets, usually create something higher than that, which in turn creates them.
- deployments automatically create replica sets.
- deployments enable rollouts.  You change the pod spec and a whole new replica set is built (pods and all).  Then the old replicaset and pods are torn down.
- deployments support "recording" so you can roll back.
- within a namespace, all resource names must be unique
- ResourceQuota (RQ), divide cluster resources within namespaces.
- service: does load balancing, has cluster IP, selector, target port refers to backend apps.  port is what the service exposes.
- kubeproxy monitors API server of master.
- updates IP tables on host when a service is added or removed or updated, and with endpoints too.
- kubelet automatically sets environment variables in all new pods, for references to active services.
  - therefore, services must be defined before that pod starts, not ideal
- k8s can support DNS (add-on) within namespace, pods can reach service names.  between namespaces, pods need to use a suffix:
    - `<svc-name>.<namespace>.svc.cluster.local`
- DNS service discovery is recommended approach.
- services can map to external entitites.
- serviceType: clusterIP: svc has IP, only accessible within the cluster
- svctype nodeport: exposes a port on the host machine, aka the worker node.  assigned randomly if not specified.
- so nodeport is "publycly" accessable (or more accessable), the port is exposed on all worker nodes, and points to the same service.
    - can use "reverse proxy" to access apps.
- use servicetype loadbalancer when the cloud provider is supplying the load balancer, in other words, there is an external load balancer.
- externalIP: target port can refer to a named port on the backend pod.
- services can support TCP and UDP.
- services can point to things external to your k8s cluster, you can point them to an external IP
- service type external name lets you establish a DNS CNAME, basically an alias, it can point to external endpoints by DNS names,
  good idea for establishing database aliases.
- types of controllers: deployment, replicaset, replicationcontroller (deprecated), job, cronjob, statefulset.
- k8s objects can have labels
- annotations can't be used in k8s commands, but labels can.
- annotations are meant for 3rd party tools.
- nodes can have taints, pods can have tolerations.
- flannel is the easiest network layer to use with k8s, but doesn't support secure protocols.
- Calico supports secure protocols.
- kubectl has contexts: pair of cluster and credentials.  so you could have one kube-config to reach all contexts.  No need for separate ones.
- $kubectl config use-context prod
- pod scheduling: quota restrictions first considered, then taints and tolerations.
- etcd stores distributed state in a B-tree key-value store.
- cloud controller manager vs. kube-controller manager.  cloud controller interacts with external agents.
- kube-proxy manages network connectivity between nodes and containers.
- kubelet ensures containers are runnint.
- supervisord: monitors kubelet and docker and restarts them if necessary.
- fluentd is used for cluster loging
- pod-shares one IP
- services: flexible and scalable, reconnects if resources are replaced.
- services can be used as load balancers, can expose node ports.
- service can handle access policies, resource control, security.
- you can list APIs of k8s: `https://<host>/apis`
- you can check access of others: `kubectl auth can-i`
- k8s uses optimistic concurrency, when you write to it there is no locking, it uses "resourceVersion" to determine if a simultaneous 
  change has been made, and returns a conflict if that happened.
- annotations are used by people or third party controllers.  They cannot be used in searches, or in selecting resources.  like when a service selects pods.
- labels can be used as selectors for resource queries
- you can use `kubectl annotate ..`
- you can make kubectl verbose `--v=9`
- 9 is the max verbosity
- `kubectl config view`: basic server info
- alpha releases are the bleeding edge, newest and least well tested, they could be buggy.  They are disabled by default.
- "beta" is more tested, stable, hardened release.  enabled by default.  Not completely stable.  They maintain backwards compatibility.
- "v1" or "vX" is most stable.
- daemonsets ensure each node ha a pod running
- statefulsets bind a container to a node
- turn on/off scheduling to a node with `kubectl cordon/uncordon`
- resource qoutas: limit number of pods, cpu, and more
- curl .../apis
- replicasets: orchestrates pod lifecycles/updates
- vs. replication controllsers? rs supports selectors.
- deployments manage RSs: gives more flexibility with upgrades and administration: `kubectl get ds`
- SS vs. Dep: ss considers each pod unique, and keeps pods ordered in deployment.  SS has stable storage, stable network ID, and a 
  consistent ordinal
    - deployment is not in parallel, it's in sequence.
- HPA: horizontal pod autoscaler, scales RS, RC, Deployments based on a target CPU.
- CA: cluster autoscaler: adds/removes nodes
    - triggered by an inability to deploy a pod, or low utilization.
- VPA: is under development
- job: has a completion number, how many must finish before job is complete, and parallelism.
- jobs are in the "batch" API.
- jobs have 3 concurrency policy options: allow, forbid, replace
- when you update a deployment, it generates a new replicaset, which generates new pods.
- RC: rolling updates client side, deployment: rolling updates server side.
- generation: how many times the object has been updated.
- resourceversion: used in etcd for concurrency changes to database cause that to change
- RS selector: all criteria must match a pod for an RS to try controlling it.
- You can set DNS Policy to kube-DNS or default.
- you can have termination messages written to docker logs, or a file.
- default DNS uses the host for DNS resolution.
- you can pass security context to pods for thins like SELinux, AppArmor, etc.
- kubectl scale deploy/fubar --replicas=4
- kubectl set image ...
- kubectl rollout history: has a histroy of changes
- kubectl rollout undo: does a rollback
- you must use "--record" to keep rollback history in annotations
- you can pause/resume a rollout
- kubectl rolling-update: works with RCs from client side, so it stops if you cloud your client.
- kubectl run automatically assigns "run" label.
- to query using a label use `-l <label>`, to have a label shown in results use `-L<labelkey>`
- you can add labels after it's built: `kubectl label`

- services: can expose resources internally or externally
- can connect internal resources to an external resource.
-provide automatic load balancing with label query
- headless service: no fixed ip, no load balancing
- services use iptables
- if you want an update to not route traffic to new pods, use a different label for them, and when the rollout is done update the prior service

- "kubectl expose" creates a service for a deployment
- "nodeport" means the host itself has exposed that port
- targetport can even be a string, the name of the port on the pods.
- typically the nodeport is randomly generated, 31k+
- services can be used to point to services in other namespaces.
- clusterip: only internal access, has ip
- nodeport: provides a static ip (is cluster ip not static?)
- load balancer: can pass requests to a plugin from your cloud provider, address is public, it's load balanced.
- ExternalName: points to external resources.
- "kubectl proxy" builds a local kube proxy, now you can access k8s resources at localhost
- kube-dns has 3 containers
- services automatically registered in kube-dns
- volumes share life of pod not containers.
- persistent volumes have a separate lifetime from pods.
- they are not deleted when thepod dies.
- multiple containers in a pod can mount the same volume, each container can have write access
- 3 access modes to a volume: RWO, ROX, RWX, no such thing as ROO
- "emptyDir" is a volume, it's an easy way for containers in a pod to communicate with each other.  it is not persisted after the pod dies.
- gitrepo is a volume type.
- hostpath: must already exist on host
- 4 phases of PV: provisioning, binding, use, releasing
- reclaim: retain, delete, recycle (deprecating)
- PV not namespaced, PVC is.
- a volume type is "persistentvolumeClaim"
- storageClass can be used to auto-provision storage
- encryptionConfig allows encryption at rest, it's an alpha features.
- secrets limited to 1MB, but no limit on number of secrets.
- they occupy RAM/emmory on hosts, and stored on tmpfs.

- ingress: more efficient than having multiple load balancer services.
- can route traffic based on request host or path.

- ingress controller is separate from 8s main controllers, can have multiple controllers, requires reverse proxy, watches ingresses, updates implementation.
- multiple ingress controllers?  must use annotations to select one, or all handle each request.
- ingress can configure L3 and L7 traffic

scheduling:

- nodeselector tells scheduler that the host node must have certain values for certain labels
- it will be deprecated when affninity is stable.
- if there is no available node with those labels, the pod remains pending.
- scheduler first filters nodes using predictions, then ranks them using prioritizers, then chooses the best.
- predicates can check: does pod fit?  is there a disk conflict?  is there a port conflict?  does it have required volumes? etc.
- lets it immediately rule out nodes that can't be used.
- you can configure the order of predicates to optimize the scheduler.
- prioritizes lets it rank nodes: number of pods already, image locality, resources available, etc.
- scheduler policy file: can re-order predicates and assign weights to prioritizers, --policy-config-file.

pod affinity:

- tends to schedule pods on the same node.
- anti-affinity: tends to schedule them on separate nodes.

logging: nothing built in, use prometheus or fluentd
- may need to add sidecar for debugging
- busybox is good for debugging
- heapster can be used to monitor key metrics
- fluentd: log aggregator, common daemonset DS
- prometheus: logging, monitoring, alerting, integration with grafana, timeseries db
- `kubectl logs <pod-name>`

CRD: custom resource definition
- aggregated APIs, k8s api -> your API
- 3rd party resource, deprecated, switch to CRDs.
- adding CRD: stored in etcd, access with API server
- controller: retrieves resource continually and acts on it.
- AA aggregated APIs
- CRD scope: namespaced or cluster
- TPR: v1.8 and before.  deprecated after v1.8
- you can have CRDs validated by k8s, withuot controller
- you can tell k8s to run a finalizer on a CRD instance before it is deleted.
- metadata: finalizers
- API server must use RBAC and TLS
- federation: you have multiple k8s clusters and one "control plane" that tracks them all, you can copy objects between clusters easily.
- each k8s cluster copies their data to the federated control plane.  If a cluster fails the ojects get started on a different cluster automatically.
- can improve performance by moving objects closer to data.
- can avoid vendor lock, can move obejcts betwen k8s clusters from different vendors.
- some objects not federated
- control plane accepts API calls, passes to members
- increased bandwidth and network traffic between clusters,
- more complex, new, not 100% stable
- sync'd every 40 seconds.
- v2 of federation in v1.11+: no sprat etcd db
- uses templates and config files. can limit what clusters accept each object
- can customize objects for each cluster
- templts let you flexibly explain how to deploy each object
- "kubefed init fellowship" is used to create a federated group of clusters.  Remnd rung w/ 10gi PV
- "kubefed join"
- replicas divided among all clusters.
- namespace created on all clusters, deleted just from federated API Server
- can use federation to rebalance RSs, using an annotation.

Security

- admission control, can completely change the incoming request
- security contexts, pod security policies
- network policies let you restrict ingress traffic between different namespaces, network tool (calico, flannel)
- can determine if network policies are supported.
- 3 steps to access: authentication, authorization, admission controllers.
- ACs: check content of objects being created, and validates them
- API Server runs with TLS
- authentication: certs, tokens, username and password
- users managed externally
- system accounts for processes
- authorizaton: abac, rbac, webhooks
- attribute based access control
- rbac: role based access control, the api has groups, rules: operations act upon API groups.
- roles: a group of rules, apply to single namespace,
- clusterroles: apply to entire cluster.
- operations act on user, service accounts, or groups
- groups: cluster role binding
- security context: limits what a pod or containers in a pod can do.  Linux capabilities se at container level.
- can tell it to processes can't run as root
- PSP: pod security policies: set at cluster level, says what a pod can do, can access, and what user they can run as.
- can block "privileged" containers.
- can block using host network NS.
- can block using host PID.
- network security policies: can limit/block traffic to pods.
- can restrict a namespace or a single pod.
- some network providers don't support network policies
- network policies just say what they allow, there is an implicit deny.  If you have an empty ingress or egress policy,
  that effectively denies all of that traffic.
- they recommend having multiple policies so it's easier to undersand what each does and to disable/enable/update each separately
- 

## Helm

- Charts: like a package manager for k8s, deploys charts to k8s.
- Tiller is server side, responsible for deploying the charts.
- helm is client side, submits charts to tiller/k8s.
- you make a single tarball and put it in a repo.
- upgrade/rollback apps easily
- uses go templates, which refer to alues_file.
- typically requires a service account.
- helm list: search, repo, add, install, upgrade


## ArgoCD

## Istio
