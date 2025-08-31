
# Principles

## SOLID

- **Single responsibility:** code blocks should do only one thing, keep the scope small.  A class should have only one job.
- **Open/Closed principle:** classes should be open to extension, but closed to contributions.  
  You should be able to extend the class without modifying it.
- **Liskov substitution:** You should be able to swap one implementation for another. 
  You should be able to substitute sub-classes in for their base class.
- **Interface Segregation:** Clients should not be forced to implement interfaces and or methods that they do not use.  
  They should not be forced to depend upon things they do not use.  Large interfaces should be de-composed to smaller ones.
- **Dependency Inversion:** entities should depend upon abstractions, not concretions or specific implementations.  
  Inputs should be interfaces, not structs.
- **KISS:** keep it simple stupid.
- **DRY:** don’t repeat yourself, reduce duplication.

## Testing

- **Unit:** tests smaller blocks of code, functions, methods, or single classes.  Mocks out any heavy dependencies.
- **Integration:** Involves multiple layers of the application.  For a web app, these test by making requests to the API.
  Data storage layers should not be mocked, but can be emulated.  Often involves containers for some dependencies, like storage layers.
- **Smoke:** Read Only, interacts with a real running set of services.
- **Regression:** Tests for behavior or logic that already existed, not new functionality.  Can also be most other types of testing.
- **System:** At the highest level, tests against a real system.
- **Acceptance:** Considers if the end users find the service behavior acceptable, if it meets requirements of design.  
  _Cucumber_ is a popular framework for this.  Product owners are usually responsible for defining the test, 
  what requirement must be met.  Engineers implement it.
- **BDD:** Behavior driven development: Like TDD, but more broadly focused on behaviour of a service, 
  system, or application, not on specific code blocks.  Cucumber is an implementation for this.
- **Stress/Load:** Simulates a high amount of traffic to the service, evaluates if it runs fast enough, scales, 
  and uses an acceptable amount of resources.  JMeter or K6s are popular frameworks for this.
- **TDD:** test driven development.  Implies tests should be written before the logic that satisfies them.  
  Tests drive the way code is written.  Lets you confirm tests fail from the start so you can be certain when they are fixed.
  Stubs might need to be written first.

## Maintainability

- Prefer stateless functions over stateful services
- Prefer immutable objects over mutable ones.
- Avoid modifying input arguments unless that is very clearly the purpose of the function or method.
- Prefer to have no side affects of a function or method.
- Avoid global variables, environment variables, shared files, etc.
- Avoid depending on specific implementations, prefer depending upon interfaces.
- Keep code simple.
- Avoid duplication
- Avoid hard-coded dependencies, like on a specific filesystem, logger, local time, etc.
- There is a typical pattern: a service is instantiated once with all of its dependencies, which may also be services,
  and those things   are never modified after creation.  The service has methods that take inputs and return outputs 
  without ever modifying its internal   state, nor the input arguments.  Side affects are avoided except where it is 
  clearly the purpose, such as saving data to a database.  Always have ample test coverage.
