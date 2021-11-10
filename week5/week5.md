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
