# Testing
This document will go over the multiple types of testing.

There are two main types of testing: functional and non-functional testing.

Functional testing is a type of software testing whereby the system is tested against the functional requirements. Functions are tested by feeding them input and examining the output. It simulates actual system usage but does not make any system structure assumptions.

Non-functional testing is checking the non-functional aspects (performance, usability, reliability, etc) of a software application. It is designed to test the readiness of a system per non-functional parameters which are never addressed by functional testing.

Functional Testing:
- Unit testing
- Integration testing
- System testing
- Sanity testing
- Smoke testing
- Interface testing
- Regression testing
- Beta/Acceptance testing

Non-functional Testing:
- Performance testing
- Load testing
- Stress testing
- Volume testing
- Security testing
- Compatibility testing
- Install testing
- Recovery testing
- Reliability testing
- Usability testing
- Compliance testing
- Localization testing

## Unit testing
Focuses on smallest unit of software design. We test an individual unit or group of inter-related units. It is often done by programmer using sample unit and observing its corresponding outputs.

## Integration testing
Testing in which individual software modules are combined and tested as a group. It is conducted to evaluate the compliance of a system or component with specified functional requirements. Occurs after unit testing and before validation testing.

### Top-down integration testing
Top integrated modules are tested and the branch of the module is tested step by step until the end of the related module. It simulates the behavior of lower-level modules that are not yet integrated (stubs).

### Bottom-up integration testing
The lowest level components are tested first, then used to facilitate the testing of higher level components. The process is repeated until the component at the top of the hierarchy is tested. This approach is helpful only when all or most of the modules of the same development level are ready.

### Black-box testing
A subtype of integration testing used for validation. It ignores the internal working mechanism and focuses on the output.

### White-box testing
A subtype of integration testing used for verification. It focuses on internal mechanism being called to achieve an output.

## Regression testing
Testing changes to make sure the old functionalities still work with the new changes. It involves re-running functional and non-functional tests to ensure that previously developed software still performs correctly after a change.

### Techniques
1. Retest all - checks all the test cases on the project to check its integrity. Although it is expensive, it ensures that no errors are introduced
2. Regression test selection - reruns a part of the test suite that might get affected with the change
3. Test case prioritization - schedules test cases that are higher priority so that they are executed before the test cases with lower priority

## Smoke testing
A preliminary testing to reveal simple failures severe enough to reject a prospective software release. Smoke tests are a subset of test cases that cover the most important functionality of a component or a system, used to aid assessment of whether main functions appear to work correctly.

It is a set of tests run on each new build of a product to verify that the build is testable before the build is released into the hands of the test team.

It aims to determine whether the application is so badly broken as to make further immediate testing unnecessary. Therefore, it runs quickly, giving faster feedback rather than running more extensive test suites.

A daily build and smoke test is among industry best practices. Smoke testing is also done by testers before accepting a build for further testing.

Smoke tests can be functional tests or unit tests.

## Validation testing
The process of checking that a software meets specifications and that it fulfills its intended purpose. 

**Verification**: Are we building the product right?

**Validation**: Are we building the right product?

This test is usually done manually by QA and business stakeholders.

### Alpha testing
A type of validation testing known also as acceptance testing. It is done before the product is released to customer and typically done by QA people.

### Beta testing
Conducted by one or more end-users of the software. The version of the product is released for a limited number of users for testing in real time environment.

## Stress testing
Testing software by giving it unfavorable conditions and check how it performs under those conditions.

Examples:
- Test cases that require maximum memory or other resources are exhausted
- Test cases that may cause thrashing in a virtual operating system
- Test cases that may cause excessive disk requirement
