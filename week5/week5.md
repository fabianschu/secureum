# Security Pitfalls & Best Practices 201

## ERC20 Token Standards

### 102 - 105 **ERC20** Conformity Checks

- [ ] **`Transfer` and `transferFrom` return a boolean**. Several tokens do not return a boolean on these functions. As a result, **their calls in the contract might fail**.
- [ ] The `name`, `decimals`, and `symbol` functions are present if used. These **functions are optional** in the ERC20 standard and might not be present.
- [ ] `Decimals` **returns** a `uint8`. Several tokens **incorrectly return a uint256**. If this is the case, **ensure the value returned is below 255**.
- [ ] The token mitigates the known **ERC20 race condition**. The ERC20 standard has a known ERC20 race condition that must be mitigated to prevent attackers from stealing tokens.
  - `approve` is just to adjust an already approved amount => attacker can front run second approval and transfer twice

### 106 - 108 **ERC20** Extension Risks

- [ ] no external function calls in ERC777 `transfer`/`transferFrom` hooks => can lead to **re-entrancy** risk
- [ ] `transfer`/`transferFrom` should not take a fee => deflationary tokens can lead to unexpected behavior
- [ ] some tokens distribute interest to holders => any contract holding such tokens must take this into considerations, otherwise **interest** can be trapped

### 109 - 111 Token Contract Composition

- [ ] token contract avoids unneeded complexity
- [ ] has only a few non-token related functions
- [ ] only one address per token (to avoid breaking internal bookkeeping based on address)

### 112 - 116 Owner privileges

- [ ] token is **not upgradeable**
- [ ] token has **limited minting capabilities** => owners can abuse mintig capabilities
- [ ] token is **not pausible** => owners can trap contracts relying on pausible taukens
- [ ] owner **cannot blacklist** addresses => can trap contracts
- [ ] **team behind the token is known** and can be held responsible => anon teams => higher standard of review

### 117 - 121 Token Scarcity

- [ ] no user owns most of the supply => **distribution**
- [ ] total **supply is sufficient** => low supply cap tokens can be easily manipulated
- [ ] tokens are located in **more than a few exchanges** => otherwise if exchange compromised => problems
- [ ] no **flash-minting** allowed => substantial swings in balance and total supply => strict & comprehensive overflow checks necessary

## Other Token Standards

### 122 - 123 ERC1400

- introduces **permissioned** addresses that can **block transfer from/to** specific addresses => problem if permissioned address is malicious
- **trusted actors** can do **forced transfers** from/to specific addresses => transfer risk

### 124. ERC1644

- controller has ability to steal funds (via forced transfers)

### 125. ERC621

- total supply can be changed by trusted actors => token supply risk

### 126 - 127 ERC884

- token implementers can **cancel** an address and **move its tokens to a new address**
- tokens can only be sent to **whitelisted addresses** => token transfer risk

## Non-code related safety aspects

### 128 - 135 Guarded Launch Concepts (read medium article!)

- **asset limits:** limit the total asset value managed by a system initially upon launch and gradually increasing it
- **asset types:** limit the number of asset types that can be used in protocol initially and gradually expanding to other assets
- **user limits:** limit the total number of users that can interact with a system (e.g. whitelisted)
- **usage limits:** enforce transaction size limits, daily volume limits, per-account limits, or rate-limiting transactions
- **composability limits:** restrict the ability for a contract to be used in a composite atomic transaction by introducing time locks around core operations
- **escrows:** escrow high value transactions with time locks and a governance capability to nullify or revert transactions
- **insurance ratios:** limit the total assets and liabilities held by a system as a ratio of a built-in insurance fund
- **circuit breakers:** implement automatic mechanisms to pause a system in extreme scenarios
- **emergency shutdown** implement facilities that allow governance to shutdown new activity in the system and allow users to reclaim assets

### 136 - 137 System transparency

- [ ] **system specification**: describes how and why the different system components behave to achieve the design requirements => **without:** system implementation cannot be evaluated against the requirements for correctness
- [ ] **system documentation**: describes what (and how) the implementation of different components of the system does to achieve the specification goals (roles, functionalities, interactions of the entire system) => **without:** system implementation cannot be evaluated against the specification for correctness and one will have to rely on analyzing the implementation itself

## Coding Best Practices

### 138 - 146 Functions

- **input validation:** for all function parameters, especially if visibility is external/public
- **function arguments:** double-check that function calls at caller site use correct arguments and in right order
- **function visibility:** ensure strictest visibility is used for the required functionality
- **function modifiers:** ensure the right set of modifiers are used (in correct order) so that expected access control or validation flow is enforced
- **function return values:** ensure appropriate return values are returned from frunctions and checked at function call sites (to catch error conditions)
- **function invocation timeliness:** externally accessible functions may be called at any time or never => don't assume they will only be called at a specific time (e.g. initialization fns)
- **function invocation repetitiveness:** eternally accessible functions may be called any number of times => it's unsafe to assume they will be called only once/specific number of times
- **function invocation order:** externally accessible functions may be called in any order => unsafe to assume specific order
- **function invocation arguments:** externally accessible functions may be called with any possible arguments

### 147 - 153 Access Control

- **specification:** system actors, their access control privileges and trust assumptions are specified so that they are correctly implemented & enforced
- **implementation:** specifiec access control is implemented uniformly across all the subjects seeking access and objects being accessed

**Conditionals:** double-check that correct variables are being checked and correct operators are used

**Modifiers:**

- **missing modifiers?** access control is typically enforced using modifiers => modifiers are present on all relevant functions?
- **incorrectly implemented modifiers?** ensure the expected checks on the correct roles/addresses w/ right composition (e.g. || instead of &&)
- **incorrectly used modifiers?** correct modifiers are used on functions requiring specific access control?

**access control changes:** changes to access control (e.g. ownership) should be handled with extra security (e.b. two-step)

### 154 - 157 Comments & dead code & code quality

- **comments:** NatSpec and inline comments
- **tests:** indicate that system implementation has been validated against the specification (unit, fucntional, integration) <> good test coverage?
- **unused constructs:** unused imports, inherited functions, parameters, variables, modifiers, events or return values => remove
- **redundant constructs:** redundant code and comments => remove

### 158 - 159 Handling value

**ETH handling**

- contracts that accept/manage/transfer ETH should ensure that functions handling ETH are using _msg.value_ appropriately
- logic that depends on ETH value accounts for **less/more** ETH sent
- logic that depends on contract ETH balance accounts for different direct/indirect ways of receiving ETH (coinbase/selfdestruct)
- transfers are reentrancy safe
- extra check for access control, input validation & error handling

**Token handling**

- functions handling tokens account for different types of ERC20 tokens (ERC20 vs ERC777), deflationary/inflationary tokens, rebasing tokens and trusted/external tokens
- extra check for access control, input validation & error handling

### 160 - 163 Trust & trusted actors

- ideally: **no trusted actors**
- exception: **guarded launch** scenarios => start with trusted actors and **progressively decentralize** towards automated governance
- for trusted phase: trusted actors, their roles and capabilities should be clearly specified, implemented accordingly and documented
- **priviliged roles** should be controlled by **multisigs** (EOA = single point of failure)
- **two-step change of privileged roles:** 1. propose new address, 2. new address claims privileged role
- **time-delayed change of critical parameters:** 1. broadcast param change via event emission, 2. execute param change after time-delay => opportunity for users to exit or adjust engagement

### 164 - 191 Common Security issues

- **explicit input:** and assumption **validation** & documentation
- **configuration issues**: misconfiguration of system components such contracts, parameters, addresses and permissions may lead to security issues
- **initialization issues:** lack of initialization, initialization with incorrect values or allowing untrusted actors to initialize system params => security issues
- **cleanup issues:** forgetting to clean up old state or cleaning up incorrectly/insufficiently => securtiy issues
- **data processing issues:** processing data incorrectly => unexpected behavior => security issues
- **data validation issues** missing validation/insufficiently validating data => untrustworthy system behavior => security issues
- **numerical issues:** incorrect numerical computation => security issues
- **accounting issues:** insufficient tracking or accounting of business logic related aspects such as states, phases, permissions, roles, funds, tokens => security issues
- **access control issues:** incorrect access control related to system actors, roles, assets, permissions => security issues
- **auditing/logging issues:** incorrect/insufficient emission of events => impacts off-chain monitoring & incident response capabilities
- **cryptography issues:** incorrect/insufficient cryptography especially related to on-chain signature verification or off-chain key management
- **error reporting issues:** incorrect/insufficient detecting, reporting & handling of error
- **Denial-of-Service (DoS) issues:** if other users can access system services by modifying system parameters they might cause DoS services which affects the availability of the system => funds might get locked
- **timing issues:** incorrect assumtions on timing of user actions, state transitions or blockchain state/blocks/transactions may lead to security issues
- **ordering issues:** incorrect assumtions on ordering of user actions or system state transitions => e.g. user may accidentally call finalization fn before initialization fn
- **undefined behavior issues:** interacting w/ external components (e.g. oracles, tokens, contracts) forces system to trust or make assumptions about their correctness requiring validation of their existence and outputs without which may lead to security issues
- **trust issues:** incorrect/insufficient trust assumption about/among system acotrs & external entities => privilege escalation/abuse => security issues
- **gas issues** incorrect assumptions about gas requirements especially for loops or external calls will lead to out-of-gas exceptions => security issues (failed transfers or locked funds)
- **dependency issues:** dependency on external actors or software => trust/availability/correctness assumptions => when broken security issues
- **constant issues:** incorrect assumptions about system actors, entities, parameters being constant may lead to security issues if factors change unexpectedly
- **freshness issues:** incorrect assumptions about incentives of system/external actors to perform or not perform certain actions, e.g. incentive to liquidate positions, lack of incentive to DoS or gries system
- **clarity issues:** lack of clarity in system specification, documentation, implementation or UX/UI => incorrect expectations => security issues
- **privacy issues:** data and tx's are not private and anyone can observe contract state and track tx's => incorrect assumptions about privacy aspects can be abused which may lead to security issues
- **cloning issues:** copy/pasting code from other libraries, contracts or even different parts of the same contract may result in íncorrect code semantics for the context being copied to, copy over any vulnerabilites or miss any security fixes applied to the original code
- **business logic issues:** incorrect/insufficient assumptions about higher-order business logic being implemented in the application => differences in expected and actual behavior => security issues

### 192 - 200 Saltzer and Schroeder's Secure Design Principles

- [ ] **principle of least privilege:** ensure that various system actors have the least amount of privilege granted as required by their roles to execute their specific tasks
- [ ] **principle of separation of privilege:** ensure that critical privileges are separated across multiple actors so that there are no single points of failure/abuse => e.g. multisigs
- [ ] **principle of least common mechanism:** ensure that only the least number of security-critical modules/paths as required are shared amongst the different actora/code
- [ ] **principle of fail-safe defaults:** ensure that variables or permissions are intialized to fail-safe default values which can be made more inclusive later instead of opening upp the system to everyone including untrusted actors
- [ ] **principle of complete mediation:** ensure that any required access control is enforced along all access paths to the object or function being protected
- [ ] **principle of economy of mechanism:** ensure that contracts and functions are not overly complex or large so as to reduce readability or maintainability
- [ ] **principle of open design:** security should be derived from the strength of the design and implementation under the assumption that attackers will study their details and try to exploit them in arbitrary ways (use open source code!)
- [ ] **principle of psychological acceptability:** ensure that security aspects of smart contract interfaces and system designs/flows are user-friendly and intuitive so that users can interact w/ minimal risk
- [ ] **principle of work factor:** given the magnitude of value managed by smart contracts, it is safe to assume that byzantine attackers will risk the greates amounts of intellectual/financial/social capital possible to subvert such systems => mitigation mechanisms must factor in the highest levels of risk
- [ ] **principle of compromise recording:** ensure that smart contracts and their accompanying operational infrastructure can be monitored at all times for minimizing loss from any compromise due to vulnerabilities/exploits (events!)
