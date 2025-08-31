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
