
# Containers

- Docker is the most common.
- Reuses the kernel of the host machine.
- Isolated filesystem, operating system, installed software, runtime.
- Can mount volumes, setup networks, assign environment variables, expose ports.

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

## Docker

### Bind Mounts

## Istio

## IAM

- Principals:
    - User: root, IAM
    - Role
    - Federated User
    - Application
- **Federation:** using a third party identity provider.  The provider gives AWS your credentials.  
  Usually involves active directory and LDAP.  Okta and Microsoft Entra are also supported.
- Request Context:
    - One of:
        - **actions:** from the web console
        - **operations:** from the CLI, API, or SDK.
    - **Resource:** the object being acted upon.
    - **Principal:** The person or application making the request
    - **Environment Data:** IP, user agent, ssl status, timestamp
    - **Resource Data:** more specific information about the resource being acted upon, such as a specific instance, table, topic, etc.
- Policy types:
    - **Identity based:** Most common.  Assigns authenticated users access to resources.
    - **Resource based:** Less common.  Enables access across accounts.
- Policy Evaluation:
    - For root user, defaults to allow access.  For all other principles, it defaults to deny a request.
    - If any policy denies the request, no other policy can approve it, it stops processing and denies it.
      All policies must allow the request for it to be approved.
    - If a policy allows the action, that changes the default decision.  It will continue evaluating other 
      policies until the request is denied or not.  In short, a request must be explicitly allowed by at least one
      policy, and not denied by any other policy.
- You should not use the root user except to setup other users and roles.
- It's preferred to have users assume a role to get access to resources, because the credentials are temporary.
- **Entity:** a user or role
- **Roles:** are an identity with a set of permissions.  Human users assume a role.
- Human users get long term credentials
- Roles and Federated users are given temporary credentials.  AWS recommends always requiring temporary credentials.
- IAM users should only be created when an application cannot work with roles.
- **Entity:** IAM User or Role.  A person or application authenticates themself as an Entity.
- **Group:** A collection of IAM users.  Policies can be assigned to groups so all users have them.
- **Identity:** IAM Entity or Group.  Multiple policies can be assigned to an identity, they get merged.
- Identity Based Policies: are attached to a user or group.
- Resource based policies: are attached to a resource like a s3 bucket.  
- Federated users are not IAM users, they must assume an IAM role.
- Roles support a resource policy and an identity policy.  
    - Their resource policy is called a role trust policy, and it specifies what entities are trusted to assume that role.
    - Their identity policy states what actions that role may perform an what resources and under what conditions.
- Policies:
    - **inline:** defined directly on a user, group, or role.  They cannot be re-used elsewhere.  These are discouraged.
    - **managed:** Defined separately.
        - **AWS managed:** AWS authors and maintains these, they are simpler to use.  You cannot change them.
        - **customer managed:** You write these, they offer more control, but require more work on your part.  This is
          what I'm most interested in.  Typically you will be writing customer managed identity based policies that are
          assigned to roles.
- [Policy Format](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#access_policies-json):
    - version: specifies the version of the policy format.  Just use the latest, "2012-10-17"
    - statements: a list
        - **Effect:** Allow or Deny
        - **Principal:** only allowed in resource based policies, specifies what identity this applies to
        - **Action:** a list of actions, depends on the resource.  Can be a single string, or a list of strings.
          Values can use wildcards like "*" or "?"
        - **Resource:** What resource this policy applies to.  For resource-based policies, this might not be needed or allowed,
          depending on what resource it gets attached to.  Can be a single string or a list of strings.
          Values can use wildcards like "*" or "?".  These refer to ARNs.
        - **Condition:** states under what condition this policy is enforced.
    - The [policy reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) is also a 
      great resource.
