# Solidity

- best-practices for **layout** within a contract is the following order:
  1. state variables
  2. events
  3. modifiers
  4. constructor
  5. functions
- **SPDX License Identifier**: Solidity source files are recommended to start with a comment indicating its license. SPDX stands for Software Package Data Exchange
- **pragma**:
  1. Version:
     a) Compiler version:
     Using the version pragma does not change the version of the compiler. It also does not enable or disable features of the compiler. It just instructs the compiler to check whether its version matches the one required by the pragma. If it does not match, the compiler issues an error.
     b) ABI Coder version: choice between the two implementations of the ABI encoder and decoder
     - “pragma abicoder v1;”
       => able to encode and decode arbitrarily nested arrays and structs. It might produce less optimal code and has not received as much testing as the old encoder. This is activated by default
     - “pragma abicoder v2;”
       =>
  2. Experimental:
     a) SMTChecker
