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

### Testing

=> should expect high-level of testing & test coverage

### Static analysis

- analyzing programms without actually executing them <> testing
- can be performed on Solidity code (=> **Mythril**) or EVM bytecode (=> **Slither**)
- combination of control flow and data flow analysis

### Fuzzing

- automated software testing technique
- provide invalid, unexpected, random data as inputs
- program is monitored for exceptions such as crashes, failing built-in assertions, memory leaks
- tools: **Echidna**, **Harvey**

### Symbolic Checking

- checking for program correctness by using **symbolic inputs to represent set of states** and transitions instead of enumerating individual states/transitions separately
- does finite-state model of system meet given specification?

### Formal Verification

- proving/disproving correctness of intended algo's w/ respect to certain formal specification or property using formal methods of mathematics
- effective at detecting complex bugs which are hard to detect manually or using simpler automated tools
- requires specification of the program being verified and techniques to translate/compare the specification with the actual implementation
- tools: **Certora's Prover**, **ChainSecurity's VerX**, **Runtime Verification's KEVM**

### Manual analysis

=> only way to infer and eval business logic and application-level constraints

## Tools

TODO

## Audit Process (from 80)

1. Read **specification/documentation** => requirements, design & architecture
2. Run **fast automated tools** such as _linters / static analyzers_ to investigate common Solidity pitfalls or missing SC best-practices
3. **Manual code analysis** to understand business logic & detect volnerabilities in it
4. Run **slower but deeper automated tools**, such as _symbolic chckers, fuzzers or formal verification anylzers_
5. **Discuss** the findings from above with other auditors to identify false positives or missing analyses
6. **Convey status** to project team for clarifying questions on business logic or threat model
7. **Iterate** the above for the duration of the audit leaving some time for report writing
8. **Write report** summarizing the above with details on findings & recommendations
9. **Deliver the report** to the project team and discuss findings, severity & potential fixes
10. **Eval fixes** from the project team and verify that they indeed remove the vulnerabities identified in findings

### 1. Read Specification/Documentation

**Specification** => **Why?**

- starts with the **project's technical & business goals** and describes how the project's **design & architecture** help achieve those goals
- actual **implementation** of smart contracts = **functional manifestation** of goals, requirements, specification, design & architecture

**Documentation** => **How?**

- description of what has been implemented based on design/architectural requirements
- typically: README, NatSpec, code comments

### 2. Running static analyzers

- Slither/MythX perform control-flow and data-flow analyses on the smart contracts in the context of their detectors which encode common security pitfalls & best-practices
- findings => good starting point to detect common vulnerabilities based on well-known constraints of Solidity/EVM or Ethereum
- false positives are possible and need to be verified manually

### 3. Manual code review

- automated analyzers do not understand application-level logic and their constraints
- manual analysis is required to detect security relevant deviations in implementations vis-a-vis the specification or documentation
- auditors may need to infer business logic and their implied constraints directly from the code of from discussions with project team

### 4. Running deepear automated tools

Tools:

- fuzzers, e.g. **Echidna**
- symbolic checkers, e.g. **Manticore**
- tool suite, e.g. **MythX**
- formal verification tools, e.g. **Scribble, Certora Prover**

Reasoning:

- require understanding of project's application logic => recommended to be used at least after an initial manual code review
- analyzing the output requires significant expertise with the tools temselves, domain-specific language and sometimes even inner workings
- evaluating false-positives is sometimes challenging with these tools but true positives they discover are significant and extreme corner cases missed even by the best manual analyses

### 5. Brainstorming with other auditors

- initially brainstorm to discuss project's goals, specification/documentation and implementation
- firewall (independently) pursue assessments
- finally come together to compile findings

### 6. Discussion with project team

- having open communication channel with project team is useful to clarify
- findings may be shared with project team immediately on private repository
- if audit spans multiple weeks it may be helpful to have weekly sync up call

### 7. Report writing

- final compilation of the entire assessment => presents all aspects: scope/coverage, timeline, team/effort, summaries, tools/techniques, findings, exploit scenarios, suggested fixes, short-/long-term recomms
  - executive summary: overview w/ highlights/lowlights illustrating the number/types/severity of vulnerabilities found and an overall assessment fo risk
  - may also include description of smart contracts, inferred actors, assets, roles, permissions, interactions, threat models, existing risk mitigation measures
  - bulk of reports focuses on findings of audit
  - may also address subjective aspects of code quality, readability/auditability and other software-engineering best practices

### 8. Report delivery

- shared online document accompanied with areadout where the auditors present the report highlights to teh project team for discussion and any debate on findings and their severity ratings
- project team typically takes some time to review ther audit report and respond back with any counterpoints on findings, severities or suggested fixes
- depending on agreement project team and audit firm migh release the audit report publicly (after fixes have been made)

### 9. Evaluating fixes

- should actually mitigate the risks reported by audit
- findings may be contested as not being relevant by project
- audti firms may evaluate the specific fixes applied and confirm/deny their risk mitigation

## Manual Review Approaches

Possible approaches:

- start witha ccess control
- start with asset flow
- start with control flow
- start with data flow
- inferring constraints
- understanding dependencies
- evaluating assumptions
- evaluating security checklists

### 1. Starting with access control

**Access Control:** Most fundamental security primitive: _who_ has authorized access to _what_

- in reality smart contracts have different permissions/roles for different actors
- general classification is that of users and admins
