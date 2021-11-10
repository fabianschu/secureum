# Security Pitfalls & Best Practices 201

## ERC20 Token Standards

### 102 - 105 **ERC20** Conformity Checks

- [ ] **`Transfer` and `transferFrom` return a boolean**. Several tokens do not return a boolean on these functions. As a result, **their calls in the contract might fail**.
- [ ] The `name`, `decimals`, and `symbol` functions are present if used. These **functions are optional** in the ERC20 standard and might not be present.
- [ ] `Decimals` **returns** a `uint8`. Several tokens **incorrectly return a uint256**. If this is the case, **ensure the value returned is below 255**.
- [ ] The token mitigates the known **ERC20 race condition**. The ERC20 standard has a known ERC20 race condition that must be mitigated to prevent attackers from stealing tokens.
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
