

# CICD

- Encourages frequent code commits.
- **Continuous integration:** As soon as code is pushed, it is checked for compilation issues, 
  and unit tests are run, to ensure it works.  May review code and perform quality analysis 
  like with Sonar, PMD, linters, etc.  Artifacts may be built, like JAR files, executables, etc.
- **Continuous Development:** Deploys to a non-production environment.  Ensures that code can be deployed and is safe to deploy. 
  May run integration tests, load tests, etc.  Sometimes if this is successful, code is automatically merged.
- **Continuous Deployment:** Automates deployment to production.  More risky, so many companies don’t allow it,
  they prefer manual prod deployments.  Usually requires a plan for easy rollback.
- **Jenkins:** Java, Groovy based.  Open Source
- Github workflows

## Github Workflows

- Workflows may be triggered by events.  They are composed of jobs.  Each job runs in parallel by default, but you can 
  add dependencies between them.  - A job runs entirely on a single runner (e.g. a virtual machine or a container).  
  Jobs are composed of steps.  A step can run a script or an action.
- An action is a reusable set of steps.
- Some typical use cases: build code, test code, deploy applications, analyze code quality, add labels to issues, etc.
- They can be triggered by events, a schedule, or manually.
- Workflows must be defined in “.github/workflows” in your repository.  They are written in yaml.
- Some events: PR is opened, issue created, commit is pushed, a release is made.
- You can use github hosted runners, or self hosted runners.
- A workflow can call upon another workflow.
- Steps are run in order by default.  Since they run on the same runner, they can share information between them.
- Usually GHWF will run workflows on the branch that resulted in the event, but some triggers require the workflow 
  to be on your main branch.  It will always run the workflow version in the branch that made the trigger, 
  not necessarily the main branch.
- You can set variables at the org, repository, or workflow level.  There are environment variables and regular variables.  
  Steps can read and modify variables.  Environment variables just exist on the runner executing your job.
- Workflows support an expression syntax, `${{ expression }}`.  In that, you can refer to “contexts”, 
  which are vaguely just objects with properties, in other words it gives you access to some kind of information.  
  You may need to defend against injection attacks.  You can refer to contexts in most of your workflow, 
  while some env vars are more limited in scope.  You might need to use contexts outside of steps, and env vars within steps.
- The “if” blocks can have expressions without the surrounding “${{“ and “}}”
- You can re-use workflows.  It will act as if the called workflow was part of the repository of the caller.  
  Any references or expressions made in the called workflow get evaluated with variables and context of the caller.  
  A git checkout, for example, would check out the caller’s repository.
- Composite steps hold a sequence of steps for a job, reusable workflows can have entire jobs within them.  
  Reusable workflows can use different runners than the calling workflow, and their jobs and steps are 
  logged separately in the calling workflow, as opposed to being just one step.  With reusable workflows, 
  you cannot pass variables between the jobs and any follow-on jobs.  Composite actions cannot use secrets, reusable workflows can.
- You can create workflow templates for others, github has a bunch of templates available.
- Runners can use the OS windows, mac, or linux, but only Linux runners can execute docker based custom actions.
- Custom actions can be written in javascript, a docker container, or composite actions.  The container adds reliability 
  because it groups the environment and dependencies with the code.  Composite actions are just a sequence of steps that run as one.
- Workflow jobs can produce artifacts, which get persisted for later.  Things like compiled binaries, 
  packaged applications, test results, log files, core dumps, and code coverage are typical artifacts.  
  Use them for output of jobs, things that are different each time it runs.  
  You can upload-artifact or download-artifact in an action.  This also enables passing artifacts between jobs.
- Github runners can cache packages, modules, or dependencies that rarely change and are frequently used.  
  For example, maven jar packages, node package manager, python modules, etc. can be cached.
- You can enable notifications from workflow runs, and control if they are sent always or just on failure.
- Some useful events: pull_request, push (a commit), label, issues, schedule, workflow_dispatch (manual workflow invocation), 
  workflow_call (a reusable - workflow is invoked by another workflow), release, create (branch/tag), delete (branch/tag), 
  fork, and discussion.  Many events have activity types, which sub-classes the event.  
  Like pull_request types are opened, edited, closed, ready_for_review, etc.
- In the “on” event type, you can add conditions on “branches”, “paths”, “types” (activity types).  
  These accept crude patterns, “**” matches anything, recursively.  They each accept multiple values.  
  The workflow_dispatch (manual invocation) and workflow_call events also accept inputs.  
  Workflow_call also supports an outputs field.
- Input variables can have a default, required, description, type, and options.  Types can be boolean, string, or choice.  
  The options field is only valid with the type choice.
- Contexts: env, vars, job, jobs, steps, inputs, secrets, github, runner, strategy, and matrix.  
  The vars context holds variables defined outside the workflow like in the org or repository.  
  The env can hold values set in your workflow.

## Argo Workflows

TODO

## Jenkins

TODO
