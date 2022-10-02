# SMTChecker (solc)

[SMTChecker](https://docs.soliditylang.org/en/v0.8.17/smtchecker.html) is a model checker. SMTChecker makes uses of a [Bounded Model Checker](https://docs.soliditylang.org/en/v0.8.17/smtchecker.html#model-checking-engines) (BMC) and a [Constraint Horn Clauses engine](https://docs.soliditylang.org/en/v0.8.17/smtchecker.html#constrained-horn-clauses-chc) (CHC). A good video about [horn clauses](https://www.youtube.com/watch?v=hgw59_HBU2A) The CHC engine (that uses a Horn solver (SPACER, part of z3)) takes the whole contract into account over multiple transactions. It should be notes that the BMC (that uses an SMT solver) in SMTChecker is relatively lightweight as it only checks a function in isolation.

Somewhat outdated, but it shows te difficulties of writing a good checker: https://www.aon.com/cyber-solutions/aon_cyber_labs/exploring-soliditys-model-checker/
https://fv.ethereum.org/2021/01/18/smtchecker-and-synthesis-of-external-functions/

Using the custom Docker image because the [official solc image](https://hub.docker.com/r/ethereum/solc) doesn't include z3 and/or cvc4.

For more information about the SMTChecker see the [Solidity docs](https://docs.soliditylang.org/en/v0.8.17/smtchecker.html).

```bash
$ docker run --rm  -v <path to>/example-smart-contracts:/prj ghcr.io/enzoevers/kevm-solc:latest bash -c "solc --base-path /prj/smart-contracts --include-path /prj/smart-contracts/node_modules --include-path /prj/smart-contracts/lib  --model-checker-engine all --model-checker-solvers all --model-checker-targets all --model-checker-timeout 60000 /prj/smart-contracts/src/VeriStake.sol"
```

The expected output will look like this:

```bash
Warning: CHC: Error trying to invoke SMT solver.
  --> src/VeriStake.sol:50:33:
   |
50 |         uint256 stakedAmount =  staked[msg.sender] + amount;
   |                                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^
   
Warning: CHC: Overflow (resulting value larger than 2**256 - 1) happens here.

Counterexample:
veriToken = 0
amount = 0
duration = 2438
stakedAmount = 0
stakedUntil = 0
Transaction trace: 
VeriStake.constructor(0x0) 
State: veriToken = 0
VeriStake.stake(0, 2438){ block.timestamp: 115792089237316195423570985008687907853269984665640564039457584007913129637498, msg.sender: 0x52f6 }
veriToken.transferFrom(msg.sender, address(this), amount) -- untrusted external call
  --> src/VeriStake.sol:51:32:
   |
51 |         uint256 stakedUntil =  block.timestamp + duration;
   |                                ^^^^^^^^^^^^^^^^^^^^^^^^^^
   
Warning: CHC: 1 verification condition(s) could not be proved. Enable the model checker option "show unproved" to see all of them. Consider choosing a specific contract to be verified in order to reduce the solving problems. Consider increasing the timeout per query.

Warning: BMC: Overflow (resulting value larger than 2**256 - 1) happens here.
  --> src/VeriStake.sol:50:33:
   |
50 |         uint256 stakedAmount =  staked[msg.sender] + amount;
   |                                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^
Note: Counterexample:
  <result> = 2**256
  amount = 7720
  duration = 0
  stakedAmount = 0
  stakedUntil = 0
  staked[msg.sender] = 0xFFFFffffFFFFffffFFFFffffFFFFffffFFFFffffFFFFffffFFFFffffFFFFe1d8
  this = 0
  veriToken = 0
  
Note: Callstack:
Note:
Note that array aliasing is not supported, therefore all mapping information is erased after a mapping local variable/parameter is assigned.   You can re-introduce information using require().
Note that external function calls are not inlined, even if the source code of the function is available. This is due to the possibility that the actual called contract has the same ABI but implements the function differently.
```