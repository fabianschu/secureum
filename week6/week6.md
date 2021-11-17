# Audit Techniques & Tools 101

## Audit types

- **new audit:** for new project
- **repeat audit:** for new version of existing project being revised with new/fixed features
- **fix audit:** reviewing the fixes made to the findings from a current/prior audit
- **retainer audit:** constantly reviewing project updates
- **incident audit:** reviewing an exploit incident, root causing the indicent

## Audit Prerequisites

1. clear definition of scope (typically commit hash)
2. public/private repo
3. public/anon team
4. specification of project's design and architecture
5. documentation of project's implementaion & business logic
6. threat models and specific areas of concern
7. testing, tools used, other audits
8. timeline, effort, costs/payment
9. engagement dynamics/channels for questions/clarifications, findings, communication
10. points of contact on both sides

## Audit Findings Classification

1. **Access Control:** related to authorization of users and assessment of rights
2. **Auditing and Logging:** related to auditing of actions or logging of problems
3. **Authentication:** related to authentification of users
4. **Configuration:** related to security configurations of servers, devices or software
5. **Cryptography:** related to protecting privacy or data integrity
6. **Data Exposure:** Related to unintended exposure of sensitive information
7. **Data Validation:** related to impropoer reliance on the structure or values of data
8. **Denial of Service:** related to causing system failure
9. **Error Reporting:** related to reporting of error conditions in a secure fashion
10. **Patching:** related to keeping software up to date
11. **Session management:** related to identification of authenticated users
12. **Timing:** related to race conditions locking or order of operations
13. **Undefined Behavior:** related to undefined behavior triggered by the program

## OWASP

### Audit Findings Likelihood/Difficulty

-> How likely will vulnerability found by attacker?

1. Undetermined: difficulty
2. Low: commonly exploited
3. Medium: attackers must write an exploit, or need an in-depth knowledge of a complex system
4. High: attacker must have privileged insider access, may need to know extremely complex technical details, or must discover other weaknesses

### Audit Findings Impact

1. low
2. medium
3. high

### Audit Findings Severity

-> integrates likelihood and impact

TODO

## Audit Checklist

- [ ] resolve easy issues: enable & address every last compiler warning, increase unit and feature test coverage, remove dead-code, ctale branches, unused libraries
- [ ] document:
  1. what product does, who uses it, why and how it delivers
  2. add comments about intended behavior in-line with the code
  3. lavel and describe your tests and results, both prositive and negative
  4. include past reviews and bugs
- [ ] deliver the code batteries included:
  1. document the steps to create a build environment from scratch on a computer that is fully disconnected from your internal network
  2. incldue external dependencies
  3. document the build process, including debugging and test env
  4. document the deployment process and env (incl. specific versions of external tools & libraries)

## Audit Techniques

1. Specification analysis (manual)
2. Documentation analysis (manual)
3. Testing (automated)
4. Static analysis (automated)
5. Fuzzing (automated)
6. Symbolic checking (automated)
7. Formal verification (automated)
8. Manual analysis (manual)

### Specification Analysis

Specification descibes in detail what the project and its components are supposed to do

1. from security perspective:
   - what are assets?
   - where are they held?
   - who are the actors? what are their privileges?
   - who is allowed to access what and when? trust relationships? threat models? potential attack vectors, scenarios & mitigations?
2. analysing specs lets them evaluate assumptions made & indicate shortcomings
3. very few smart contract projects have detailed specs at first audit stage => auditors spend a lot of time inferring specification from documentation/implementation => less time for vuln assessment

### Documentation Analysis

Documentation is description of what has been implemented based on the design and architectural requirements.

1. answers _how_ something has been designed/archutected/implemented without necessarily addressing the _why_ & design/requirement goals
2. typically in the form of Readme files, combined with functional NatSpec & individual code comments
3. in many cases serves as substitute for specification & provides critical insights into assumptions, requirements & goals
4. understanding the documentation before looking at the code helpsauditors save time in inferring the architecture of the project
5. **mismatches between documentation and code** could indicate stale/poor documentation, software defects or security vulnerabilities
6. auditors are expected to encourage project team to document thoroughly
