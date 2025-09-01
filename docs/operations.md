# Operations

- **Gitops:** You leverage github to perform operations.
- **ZDT:** Zero downtime
- Deployment Strategies:
    - **Canary:** gradually shift traffic from old service to new one, monitor service metrics as you go, have automatic rollback criteria.
    - **Blue Green:** both are deployed for a while.  Flip a switch to redirect traffic.
    - **Stop the world:** Take down one, bring up the other.
    - **A/B:** More focused on user experience, a subset of users is given the new service or UI.
    - **SLI:** service level indicator, a metric that service is judged by.
    - **SLA:** service level agreement, states when a diminished service is unacceptable and has consequences like compensation.
    - **SLO:** service level objective: a goal to reach.  Usually stricter than the SLA

## Twelve factor apps

- Code must be in version control, like github.  There should be one repository per app.  Implies that monorepos are not desirable.
- Dependencies: Refers to code libraries that your app uses.  They are explicitly declared and isolated from the rest of 
  the runtime environment.  Competing dependencies cannot leak in.  Go modules, or common libraries, are declared in some dependencies file.  
  It’s more vital for code that uses a class path or similar, like java or python.  
  In golang, things are compiled so at runtime it’s not loading common libraries.
- Configuration must be separated from code.  Configuration is anything that is likely to vary between each deployment, 
  both over time and over environments.  Code should not store addresses to external services if there is a different
  value depending on the environment.  There should be no credentials in the code.  You should be able to move it to a 
  completely different environment without needing to make code updates.  Recommends using - environment variables, 
  and not storing config files in version control.
- Don’t distinguish between local and external resources, treat them all as attached resources, only connect with them by address.  
  You should not need to change code if a resource becomes local or external.
- Build, release, and run stages must be strictly separated.
- The app should be stateless and share nothing.  Don’t assume that anything cached in its memory will be available in 
  future requests.  Don’t use - sticky sessions.
- Produce entirely self contained apps, they should not depend on a separate webserver like Tomcat.  
  The application should host its own web server and bind to a port.  It can run jetty for Java, or chi for Golang.
- Scale out by having more processes, not by having more resources in one process, not by having a process do more things.
- Processes are disposable.  They should be able to start up fast, within a few seconds.  
  They should handle graceful shutdowns, cleaning up things when it receives an interrupt signal, and stopping all 
  future request handling.
- Keep your environments as similar as possible.
- Logs should be written to standard output, and thought of as event streams.  Apps should not be managing log files, rotating them, compressing them, etc.
