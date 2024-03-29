# Code report

## Slither



Compiled with solc

Number of lines: 473 (+ 0 in dependencies, + 0 in tests)

Number of assembly lines: 0

Number of contracts: 6 (+ 0 in dependencies, + 0 tests) 



Number of optimization issues: 0

Number of informational issues: 5

Number of low issues: 1

Number of medium issues: 1

Number of high issues: 4

ERCs: ERC20



For more information about the detected items see the [Slither documentation](https://github.com/crytic/slither/wiki/Detector-Documentation).


### solc-version

- Impact: `Informational`
- Confidence: `High`


```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

2 pragma solidity ^0.8.13;
```

### solc-version

- Impact: `Informational`
- Confidence: `High`


### solc-version

- Impact: `Informational`
- Confidence: `High`


```Solidity
// src/smart-contracts/interfaces/IVeriAuctionTokenForEth.sol

2 pragma solidity ^0.8.13;
```

### low-level-calls

- Impact: `Informational`
- Confidence: `High`


**In Function**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

95     function resignFromAuction() external override {
96         require(commited[msg.sender] > 0, "VeriAuctionTokenForEth (resignFromAuction): must have commited some ETH");
97 
98         if (auctionFinalized()) {
99             // Gas savings
100             uint256 _unclaimableTokenAmount = unclaimableTokenAmount;
101             _unclaimableTokenAmount += calculateClaimableAmount();
102             require(
103                 _unclaimableTokenAmount <= amountToDistribute,
104                 "VeriAuctionTokenForEth (resignFromAuction): unclaimable amount would be larger than total tokens to distribute"
105             );
106             unclaimableTokenAmount = _unclaimableTokenAmount;
107         }
108 
109         uint256 commitment = commited[msg.sender];
110         require(
111             getEthBalance() >= commitment,
112             "VeriAuctionTokenForEth (resignFromAuction): contract doesn't have enough ETH"
113         );
114 
115         // In these three lines the re-entrancy attack happens.
116         (bool transferSuccess, ) = msg.sender.call{value: commitment}("");
117         require(transferSuccess, "VeriAuctionTokenForEth (resignFromAuction): failed to send ETH");
118 
119         delete commited[msg.sender];
120     }
```

**Lines of relevance**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

116         (bool transferSuccess, ) = msg.sender.call{value: commitment}("");
```

### naming-convention

- Impact: `Informational`
- Confidence: `High`


### reentrancy-benign

- Impact: `Low`
- Confidence: `Medium`


**In Function**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

56     function depositAuctionTokens() external onlyOwner {
57         // The deployer of this auction contract should have approved the auction
58         // contract to transfer the auction token before deploying this contract.
59         auctionToken.transferFrom(msg.sender, address(this), amountToDistribute);
60         auctionStarted = true;
61     }
```

**Lines of relevance**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

59         auctionToken.transferFrom(msg.sender, address(this), amountToDistribute);
```

**Lines of relevance**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

59         auctionToken.transferFrom(msg.sender, address(this), amountToDistribute);
```

**Lines of relevance**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

60         auctionStarted = true;
```

### divide-before-multiply

- Impact: `Medium`
- Confidence: `Medium`


**In Function**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

195     function calculateClaimableAmount() public view returns (uint256 claimableAmount) {
196         require(auctionFinalized(), "VeriAuctionTokenForEth (calculateClaimableAmount): auction not finalized yet");
197 
198         // Note that if we would have used getCurrentPrice() in the contract, then someone
199         // could send eth to the contract using the selfdestruct method and make it so that
200         // no one can claim tokens due to getCurrentPrice() reverting.
201         // Even though a lot of ETH should be sent to the contract, theoratically it is possible.
202         //
203         //  Example:
204         // claimableAmount = (commited[msg.sender] * 10**auctionTokenDecimals) / getCurrentPrice();
205 
206         // We know that share will always be <= 1e18
207         // The constructor already takes care of preventing an overflow in (share * amountToDistribute)
208         uint256 share = (commited[msg.sender] * 1e18) / finalEthBalance;
209         claimableAmount = (share * amountToDistribute) / 1e18;
210     }
```

**Lines of relevance**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

208         uint256 share = (commited[msg.sender] * 1e18) / finalEthBalance;
```

**Lines of relevance**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

209         claimableAmount = (share * amountToDistribute) / 1e18;
```

### reentrancy-eth

- Impact: `High`
- Confidence: `Medium`


**In Function**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

95     function resignFromAuction() external override {
96         require(commited[msg.sender] > 0, "VeriAuctionTokenForEth (resignFromAuction): must have commited some ETH");
97 
98         if (auctionFinalized()) {
99             // Gas savings
100             uint256 _unclaimableTokenAmount = unclaimableTokenAmount;
101             _unclaimableTokenAmount += calculateClaimableAmount();
102             require(
103                 _unclaimableTokenAmount <= amountToDistribute,
104                 "VeriAuctionTokenForEth (resignFromAuction): unclaimable amount would be larger than total tokens to distribute"
105             );
106             unclaimableTokenAmount = _unclaimableTokenAmount;
107         }
108 
109         uint256 commitment = commited[msg.sender];
110         require(
111             getEthBalance() >= commitment,
112             "VeriAuctionTokenForEth (resignFromAuction): contract doesn't have enough ETH"
113         );
114 
115         // In these three lines the re-entrancy attack happens.
116         (bool transferSuccess, ) = msg.sender.call{value: commitment}("");
117         require(transferSuccess, "VeriAuctionTokenForEth (resignFromAuction): failed to send ETH");
118 
119         delete commited[msg.sender];
120     }
```

**Lines of relevance**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

116         (bool transferSuccess, ) = msg.sender.call{value: commitment}("");
```

**Lines of relevance**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

119         delete commited[msg.sender];
```

### unchecked-transfer

- Impact: `High`
- Confidence: `Medium`


**In Function**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

153     function claimUndistributedAuctionTokens() external onlyOwner {
154         uint256 tokensToSend = unclaimableTokenAmount;
155         delete unclaimableTokenAmount;
156 
157         // The transfer will fail on insufficient balance
158         auctionToken.transfer(owner(), tokensToSend);
159     }
```

**Lines of relevance**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

158         auctionToken.transfer(owner(), tokensToSend);
```

### unchecked-transfer

- Impact: `High`
- Confidence: `Medium`


**In Function**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

56     function depositAuctionTokens() external onlyOwner {
57         // The deployer of this auction contract should have approved the auction
58         // contract to transfer the auction token before deploying this contract.
59         auctionToken.transferFrom(msg.sender, address(this), amountToDistribute);
60         auctionStarted = true;
61     }
```

**Lines of relevance**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

59         auctionToken.transferFrom(msg.sender, address(this), amountToDistribute);
```

### unchecked-transfer

- Impact: `High`
- Confidence: `Medium`


**In Function**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

137     function claimTokens() external override {
138         require(auctionFinalized(), "VeriAuctionTokenForEth (claimTokens): Auction not finalized yet");
139 
140         uint256 claimableAmount = calculateClaimableAmount();
141 
142         // The writer of this functino might think that using the delete before the transfer prevents
143         // a re-entrancy attack. It actually succeedes in doing that. But it can still be part of another
144         // attack.
145         // These two lines can also be part of the re-entrancy attack in resignFromAuction().
146         // Than not only ETH was stolen, but the attacker would also get its share of the auction tokens.
147         delete commited[msg.sender];
148         auctionToken.transfer(msg.sender, claimableAmount);
149     }
```

**Lines of relevance**

```Solidity
// src/smart-contracts/VeriAuctionTokenForEth_problems.sol

148         auctionToken.transfer(msg.sender, claimableAmount);
```
## SMTChecker



=================================================================

Pulling image ghcr.io/byont-ventures/analysis-toolbox:01-02-2023_11-18

=================================================================



01-02-2023_11-18: Pulling from byont-ventures/analysis-toolbox

Digest: sha256:7d79bea5976a1d036351a2e240413674eef6beaac6bd4376648f1d54c285d196

Status: Image is up to date for ghcr.io/byont-ventures/analysis-toolbox:01-02-2023_11-18

ghcr.io/byont-ventures/analysis-toolbox:01-02-2023_11-18



=================================================================

Running SMTChecker

=================================================================



Warning: Assertion checker does not yet implement this operator.

  --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:77:33:

   |

77 |             type(uint256).max / 10**auctionTokenDecimals >= getEthBalance(),

   |                                 ^^^^^^^^^^^^^^^^^^^^^^^^



Warning: Assertion checker does not yet implement this operator.

   --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:186:33:

    |

186 |             type(uint256).max / 10**auctionTokenDecimals >= getEthBalance(),

    |                                 ^^^^^^^^^^^^^^^^^^^^^^^^



Warning: Assertion checker does not yet implement this operator.

   --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:190:51:

    |

190 |         pricePerAuctionToken = (getEthBalance() * 10**auctionTokenDecimals) / amountToDistribute;

    |                                                   ^^^^^^^^^^^^^^^^^^^^^^^^



Warning: Assertion checker does not yet implement this operator.

  --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:34:36:

   |

34 |             _amountToDistribute >= 10**auctionTokenDecimals,

   |                                    ^^^^^^^^^^^^^^^^^^^^^^^^



Warning: CHC: Error trying to invoke SMT solver.

  --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:77:13:

   |

77 |             type(uint256).max / 10**auctionTokenDecimals >= getEthBalance(),

   |             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



Warning: CHC: Error trying to invoke SMT solver.

  --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:81:41:

   |

81 |             type(uint256).max / 1e18 >= commited[msg.sender] + msg.value,

   |                                         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



Warning: CHC: Division by zero happens here.

Counterexample:

auctionToken = 0, amountToDistribute = 0, auctionTokenDecimals = 0, auctionStarted = false, finalEthBalance = 0, unclaimableTokenAmount = 0, _owner = 0x0

veriTokenAddress = 0x0

_amountToDistribute = 0



Transaction trace:

VeriAuctionTokenForEth_problems.constructor(0x0, 0)

  --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:38:13:

   |

38 |             type(uint256).max / _amountToDistribute >= 1e18,

   |             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



Warning: CHC: Division by zero happens here.

Counterexample:

auctionToken = 0, amountToDistribute = 1, auctionTokenDecimals = 0, auctionStarted = false, finalEthBalance = 0, unclaimableTokenAmount = 0, _owner = 0x0

pricePerAuctionToken = 0



Transaction trace:

VeriAuctionTokenForEth_problems.constructor(0x0, 1)

State: auctionToken = 0, amountToDistribute = 1, auctionTokenDecimals = 0, auctionStarted = false, finalEthBalance = 0, unclaimableTokenAmount = 0, _owner = 0x0

VeriAuctionTokenForEth_problems.getCurrentPrice()

   --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:186:13:

    |

186 |             type(uint256).max / 10**auctionTokenDecimals >= getEthBalance(),

    |             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



Warning: CHC: Overflow (resulting value larger than 2**256 - 1) happens here.

Counterexample:

auctionToken = 0, amountToDistribute = 1, auctionTokenDecimals = 0, auctionStarted = false, finalEthBalance = 0, unclaimableTokenAmount = 0, _owner = 0x0

pricePerAuctionToken = 0



Transaction trace:

VeriAuctionTokenForEth_problems.constructor(0x0, 1)

State: auctionToken = 0, amountToDistribute = 1, auctionTokenDecimals = 0, auctionStarted = false, finalEthBalance = 0, unclaimableTokenAmount = 0, _owner = 0x0

VeriAuctionTokenForEth_problems.getCurrentPrice()

    VeriAuctionTokenForEth_problems.getEthBalance() -- internal call

    VeriAuctionTokenForEth_problems.getEthBalance() -- internal call

   --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:190:33:

    |

190 |         pricePerAuctionToken = (getEthBalance() * 10**auctionTokenDecimals) / amountToDistribute;

    |                                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



Warning: CHC: 10 verification condition(s) could not be proved. Enable the model checker option "show unproved" to see all of them. Consider choosing a specific contract to be verified in order to reduce the solving problems. Consider increasing the timeout per query.



Warning: BMC: Division by zero happens here.

  --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:77:13:

   |

77 |             type(uint256).max / 10**auctionTokenDecimals >= getEthBalance(),

   |             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Note: Counterexample:

  <result> = 0

  _owner = 0

  amountToDistribute = 0

  auctionStarted = true

  auctionToken = 0

  auctionTokenDecimals = 0

  finalEthBalance = 0

  finialized = false

  unclaimableTokenAmount = 0



Note: Callstack:

Note: 

Note that array aliasing is not supported, therefore all mapping information is erased after a mapping local variable/parameter is assigned.

You can re-introduce information using require().



Warning: BMC: Overflow (resulting value larger than 2**256 - 1) happens here.

  --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:81:41:

   |

81 |             type(uint256).max / 1e18 >= commited[msg.sender] + msg.value,

   |                                         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Note: Counterexample:

   = 0

  <result> = 2**256

  _owner = 2**160 - 1

  address(this).balance = 0

  amountToDistribute = 2**256 - 1

  auctionStarted = true

  auctionToken = 0

  auctionTokenDecimals = 0

  commited[msg.sender] = 2**256 - 1

  finalEthBalance = 0

  finialized = false

  this = 2**160 - 1

  unclaimableTokenAmount = 2**256 - 1



Note: Callstack:

Note: 

Note that array aliasing is not supported, therefore all mapping information is erased after a mapping local variable/parameter is assigned.

You can re-introduce information using require().



Warning: BMC: Overflow (resulting value larger than 2**256 - 1) happens here.

   --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:208:26:

    |

208 |         uint256 share = (commited[msg.sender] * 1e18) / finalEthBalance;

    |                          ^^^^^^^^^^^^^^^^^^^^^^^^^^^

Note: Counterexample:

  <result> = 0x0100000000000000000000000000000000000000000000000005C5e69957480000

  _owner = 0

  _unclaimableTokenAmount = 0

  amountToDistribute = 0

  auctionToken = 0

  auctionTokenDecimals = 0

  claimableAmount = 0

  commited[msg.sender] = 0x12725Dd1d243ABa0e75FE645cc4873f9e65AFE688c928E1f22

  commitment = 0

  finalEthBalance = 1

  finialized = true

  share = 0

  transferSuccess = false

  unclaimableTokenAmount = 0



Note: Callstack:

Note:

   --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:101:40:

    |

101 |             _unclaimableTokenAmount += calculateClaimableAmount();

    |                                        ^^^^^^^^^^^^^^^^^^^^^^^^^^

Note: 

Note that array aliasing is not supported, therefore all mapping information is erased after a mapping local variable/parameter is assigned.

You can re-introduce information using require().

Note that external function calls are not inlined, even if the source code of the function is available. This is due to the possibility that the actual called contract has the same ABI but implements the function differently.



Warning: BMC: Overflow (resulting value larger than 2**256 - 1) happens here.

   --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:209:28:

    |

209 |         claimableAmount = (share * amountToDistribute) / 1e18;

    |                            ^^^^^^^^^^^^^^^^^^^^^^^^^^

Note: Counterexample:

  <result> = 2**256

  _owner = 0

  _unclaimableTokenAmount = 0

  amountToDistribute = 0x80 * 2**248

  auctionToken = 0

  auctionTokenDecimals = 0

  claimableAmount = 0

  commited[msg.sender] = 1

  commitment = 0

  finalEthBalance = 0x06F05b59D3B20000

  finialized = true

  share = 2

  transferSuccess = false

  unclaimableTokenAmount = 0



Note: Callstack:

Note:

   --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:101:40:

    |

101 |             _unclaimableTokenAmount += calculateClaimableAmount();

    |                                        ^^^^^^^^^^^^^^^^^^^^^^^^^^

Note: 

Note that array aliasing is not supported, therefore all mapping information is erased after a mapping local variable/parameter is assigned.

You can re-introduce information using require().

Note that external function calls are not inlined, even if the source code of the function is available. This is due to the possibility that the actual called contract has the same ABI but implements the function differently.



Warning: BMC: Overflow (resulting value larger than 2**256 - 1) happens here.

   --> src/smart-contracts/VeriAuctionTokenForEth_problems.sol:101:13:

    |

101 |             _unclaimableTokenAmount += calculateClaimableAmount();

    |             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Note: Counterexample:

  <result> = 2**256

  _owner = 0

  _unclaimableTokenAmount = 0xFFFFffffFFFFffed8DA22e2dBC545f18A019ba33B78C0619A50197736D71e0df

  amountToDistribute = 2**256 - 1

  auctionToken = 2**160 - 1

  auctionTokenDecimals = 0

  claimableAmount = 0x12725Dd1d243ABa0e75FE645cc4873f9e65AFE688c928E1f21

  commited[msg.sender] = 1

  commitment = 0

  finalEthBalance = 0x0DE0b6b3A7640000

  finialized = true

  share = 1

  transferSuccess = false

  unclaimableTokenAmount = 0xFFFFffffFFFFffed8DA22e2dBC545f18A019ba33B78C0619A50197736D71e0df



Note: Callstack:

Note: 

Note that array aliasing is not supported, therefore all mapping information is erased after a mapping local variable/parameter is assigned.

You can re-introduce information using require().

Note that external function calls are not inlined, even if the source code of the function is available. This is due to the possibility that the actual called contract has the same ABI but implements the function differently.



## Mythril



```json

{

  "issues": [

    {

      "description": {

        "head": "Any sender can withdraw Ether from the contract account.",

        "tail": "Arbitrary senders other than the contract creator can profitably extract Ether from the contract account. Verify the business logic carefully and make sure that appropriate security controls are in place to prevent unexpected loss of funds."

      },

      "extra": {

        "discoveryTime": 481277480840,

        "testCases": [

          {

            "initialState": {

              "accounts": {

                "0x0": {

                  "balance": "0x40000000000000003",

                  "code": "6080604052600436106100e85760003560e01c8063715018a61161008a578063b1111f1911610059578063b1111f1914610241578063eb91d37e14610258578063ed4460ef14610283578063f2fde38b146102ae576100e8565b8063715018a6146101bd57806388463329146101d45780638da5cb5b146101eb57806399fdb32014610216576100e8565b806345dc13f3116100c657806345dc13f31461015a57806348c54b9d146101645780634bb278f31461017b5780635054f45814610192576100e8565b80630bd8a1d0146100ed5780632a79c611146101185780633ff62dd114610143575b600080fd5b3480156100f957600080fd5b506101026102d7565b60405161010f91906110d3565b60405180910390f35b34801561012457600080fd5b5061012d6102e3565b60405161013a9190611107565b60405180910390f35b34801561014f57600080fd5b5061015861032a565b005b6101626103e8565b005b34801561017057600080fd5b506101796106db565b005b34801561018757600080fd5b50610190610813565b005b34801561019e57600080fd5b506101a761087a565b6040516101b49190611107565b60405180910390f35b3480156101c957600080fd5b506101d261096c565b005b3480156101e057600080fd5b506101e9610980565b005b3480156101f757600080fd5b50610200610c18565b60405161020d9190611163565b60405180910390f35b34801561022257600080fd5b5061022b610c41565b60405161023891906111dd565b60405180910390f35b34801561024d57600080fd5b50610256610c65565b005b34801561026457600080fd5b5061026d610d4b565b60405161027a9190611107565b60405180910390f35b34801561028f57600080fd5b50610298610ebf565b6040516102a59190611107565b60405180910390f35b3480156102ba57600080fd5b506102d560048036038101906102d09190611229565b610ee3565b005b60008060015411905090565b6000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054905090565b610332610f66565b600060035490506003600090557f000000000000000000000000000000000000000000000000000000000000000073ffffffffffffffffffffffffffffffffffffffff1663a9059cbb610383610c18565b836040518363ffffffff1660e01b81526004016103a1929190611256565b6020604051808303816000875af11580156103c0573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906103e491906112ab565b5050565b600060149054906101000a900460ff16610437576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161042e9061135b565b60405180910390fd5b61043f6102d7565b1561047f576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610476906113ed565b60405180910390fd5b600034116104c2576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016104b9906114a5565b60405180910390fd5b6104ca610fe4565b7f0000000000000000000000000000000000000000000000000000000000000000600a6104f79190611627565b7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff61052291906116a1565b1015610563576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161055a9061176a565b60405180910390fd5b34600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020546105ae919061178a565b670de0b6b3a76400007fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff6105e291906116a1565b1015610623576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161061a90611856565b60405180910390fd5b34600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828254610672919061178a565b925050819055503373ffffffffffffffffffffffffffffffffffffffff167f9cff2c0a4d5be51d8a883d8301a9be115ec08ce14e5fe70c21c3f5f507cca33f346106ba610fe4565b6106c2610d4b565b6040516106d193929190611876565b60405180910390a2565b6106e36102d7565b610722576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016107199061191f565b60405180910390fd5b600061072c61087a565b9050600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600090557f000000000000000000000000000000000000000000000000000000000000000073ffffffffffffffffffffffffffffffffffffffff1663a9059cbb33836040518363ffffffff1660e01b81526004016107cc929190611256565b6020604051808303816000875af11580156107eb573d6000803e3d6000fd5b505050506040513d601f19601f8201168201806040525081019061080f91906112ab565b5050565b61081b610f66565b600060149054906101000a900460ff1661086a576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610861906119b1565b60405180910390fd5b610872610fe4565b600181905550565b60006108846102d7565b6108c3576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016108ba90611a69565b60405180910390fd5b6000600154670de0b6b3a7640000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205461091b9190611a89565b61092591906116a1565b9050670de0b6b3a76400007f00000000000000000000000000000000000000000000000000000000000000008261095c9190611a89565b61096691906116a1565b91505090565b610974610f66565b61097e6000610fec565b565b6000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205411610a02576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016109f990611b63565b60405180910390fd5b610a0a6102d7565b15610a975760006003549050610a1e61087a565b81610a29919061178a565b90507f0000000000000000000000000000000000000000000000000000000000000000811115610a8e576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610a8590611c41565b60405180910390fd5b80600381905550505b6000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054905080610ae4610fe4565b1015610b25576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610b1c90611cf9565b60405180910390fd5b60003373ffffffffffffffffffffffffffffffffffffffff1682604051610b4b90611d4a565b60006040518083038185875af1925050503d8060008114610b88576040519150601f19603f3d011682016040523d82523d6000602084013e610b8d565b606091505b5050905080610bd1576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610bc890611dd1565b60405180910390fd5b600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600090555050565b60008060009054906101000a900473ffffffffffffffffffffffffffffffffffffffff16905090565b7f000000000000000000000000000000000000000000000000000000000000000081565b610c6d610f66565b7f000000000000000000000000000000000000000000000000000000000000000073ffffffffffffffffffffffffffffffffffffffff166323b872dd33307f00000000000000000000000000000000000000000000000000000000000000006040518463ffffffff1660e01b8152600401610cea93929190611df1565b6020604051808303816000875af1158015610d09573d6000803e3d6000fd5b505050506040513d601f19601f82011682018060405250810190610d2d91906112ab565b506001600060146101000a81548160ff021916908315150217905550565b6000807f000000000000000000000000000000000000000000000000000000000000000011610daf576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610da690611ec0565b60405180910390fd5b610db7610fe4565b7f0000000000000000000000000000000000000000000000000000000000000000600a610de49190611627565b7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff610e0f91906116a1565b1015610e50576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610e4790611f78565b60405180910390fd5b7f00000000000000000000000000000000000000000000000000000000000000007f0000000000000000000000000000000000000000000000000000000000000000600a610e9e9190611627565b610ea6610fe4565b610eb09190611a89565b610eba91906116a1565b905090565b7f000000000000000000000000000000000000000000000000000000000000000081565b610eeb610f66565b600073ffffffffffffffffffffffffffffffffffffffff168173ffffffffffffffffffffffffffffffffffffffff1603610f5a576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610f519061200a565b60405180910390fd5b610f6381610fec565b50565b610f6e6110b0565b73ffffffffffffffffffffffffffffffffffffffff16610f8c610c18565b73ffffffffffffffffffffffffffffffffffffffff1614610fe2576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610fd990612076565b60405180910390fd5b565b600047905090565b60008060009054906101000a900473ffffffffffffffffffffffffffffffffffffffff169050816000806101000a81548173ffffffffffffffffffffffffffffffffffffffff021916908373ffffffffffffffffffffffffffffffffffffffff1602179055508173ffffffffffffffffffffffffffffffffffffffff168173ffffffffffffffffffffffffffffffffffffffff167f8be0079c531659141344cd1fd0a4f28419497f9722a3daafe3b4186f6b6457e060405160405180910390a35050565b600033905090565b60008115159050919050565b6110cd816110b8565b82525050565b60006020820190506110e860008301846110c4565b92915050565b6000819050919050565b611101816110ee565b82525050565b600060208201905061111c60008301846110f8565b92915050565b600073ffffffffffffffffffffffffffffffffffffffff82169050919050565b600061114d82611122565b9050919050565b61115d81611142565b82525050565b60006020820190506111786000830184611154565b92915050565b6000819050919050565b60006111a361119e61119984611122565b61117e565b611122565b9050919050565b60006111b582611188565b9050919050565b60006111c7826111aa565b9050919050565b6111d7816111bc565b82525050565b60006020820190506111f260008301846111ce565b92915050565b600080fd5b61120681611142565b811461121157600080fd5b50565b600081359050611223816111fd565b92915050565b60006020828403121561123f5761123e6111f8565b5b600061124d84828501611214565b91505092915050565b600060408201905061126b6000830185611154565b61127860208301846110f8565b9392505050565b611288816110b8565b811461129357600080fd5b50565b6000815190506112a58161127f565b92915050565b6000602082840312156112c1576112c06111f8565b5b60006112cf84828501611296565b91505092915050565b600082825260208201905092915050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a2041756374696f6e206e6f742073746172746564000000000000000000602082015250565b60006113456037836112d8565b9150611350826112e9565b604082019050919050565b6000602082019050818103600083015261137481611338565b9050919050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a2041756374696f6e2069732066696e616c697a65640000000000000000602082015250565b60006113d76038836112d8565b91506113e28261137b565b604082019050919050565b60006020820190508181036000830152611406816113ca565b9050919050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a20636f6d6d69746d656e74206d7573742062652067726561746572207460208201527f68616e2030000000000000000000000000000000000000000000000000000000604082015250565b600061148f6045836112d8565b915061149a8261140d565b606082019050919050565b600060208201905081810360008301526114be81611482565b9050919050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052601160045260246000fd5b60008160011c9050919050565b6000808291508390505b600185111561154b57808604811115611527576115266114c5565b5b60018516156115365780820291505b8081029050611544856114f4565b945061150b565b94509492505050565b6000826115645760019050611620565b816115725760009050611620565b81600181146115885760028114611592576115c1565b6001915050611620565b60ff8411156115a4576115a36114c5565b5b8360020a9150848211156115bb576115ba6114c5565b5b50611620565b5060208310610133831016604e8410600b84101617156115f65782820a9050838111156115f1576115f06114c5565b5b611620565b6116038484846001611501565b9250905081840481111561161a576116196114c5565b5b81810290505b9392505050565b6000611632826110ee565b915061163d836110ee565b925061166a7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8484611554565b905092915050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052601260045260246000fd5b60006116ac826110ee565b91506116b7836110ee565b9250826116c7576116c6611672565b5b828204905092915050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a20776f756c6420726573756c7420696e20746f6f206d7563682045544860208201527f20696e207468652061756374696f6e0000000000000000000000000000000000604082015250565b6000611754604f836112d8565b915061175f826116d2565b606082019050919050565b6000602082019050818103600083015261178381611747565b9050919050565b6000611795826110ee565b91506117a0836110ee565b92508282019050808211156117b8576117b76114c5565b5b92915050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a20776f756c6420726573756c74206973206120746f6f206c617267652060208201527f636f6d6d69746d656e7400000000000000000000000000000000000000000000604082015250565b6000611840604a836112d8565b915061184b826117be565b606082019050919050565b6000602082019050818103600083015261186f81611833565b9050919050565b600060608201905061188b60008301866110f8565b61189860208301856110f8565b6118a560408301846110f8565b949350505050565b7f5665726941756374696f6e546f6b656e466f724574682028636c61696d546f6b60008201527f656e73293a2041756374696f6e206e6f742066696e616c697a65642079657400602082015250565b6000611909603f836112d8565b9150611914826118ad565b604082019050919050565b60006020820190508181036000830152611938816118fc565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202866696e616c697a6560008201527f293a2041756374696f6e206e6f74207374617274656400000000000000000000602082015250565b600061199b6036836112d8565b91506119a68261193f565b604082019050919050565b600060208201905081810360008301526119ca8161198e565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202863616c63756c617460008201527f65436c61696d61626c65416d6f756e74293a2061756374696f6e206e6f74206660208201527f696e616c697a6564207965740000000000000000000000000000000000000000604082015250565b6000611a53604c836112d8565b9150611a5e826119d1565b606082019050919050565b60006020820190508181036000830152611a8281611a46565b9050919050565b6000611a94826110ee565b9150611a9f836110ee565b9250828202611aad816110ee565b91508282048414831517611ac457611ac36114c5565b5b5092915050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a206d757374206861766520636f6d6d69746564207360208201527f6f6d652045544800000000000000000000000000000000000000000000000000604082015250565b6000611b4d6047836112d8565b9150611b5882611acb565b606082019050919050565b60006020820190508181036000830152611b7c81611b40565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a20756e636c61696d61626c6520616d6f756e74207760208201527f6f756c64206265206c6172676572207468616e20746f74616c20746f6b656e7360408201527f20746f2064697374726962757465000000000000000000000000000000000000606082015250565b6000611c2b606e836112d8565b9150611c3682611b83565b608082019050919050565b60006020820190508181036000830152611c5a81611c1e565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a20636f6e747261637420646f65736e27742068617660208201527f6520656e6f756768204554480000000000000000000000000000000000000000604082015250565b6000611ce3604c836112d8565b9150611cee82611c61565b606082019050919050565b60006020820190508181036000830152611d1281611cd6565b9050919050565b600081905092915050565b50565b6000611d34600083611d19565b9150611d3f82611d24565b600082019050919050565b6000611d5582611d27565b9150819050919050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a206661696c656420746f2073656e64204554480000602082015250565b6000611dbb603e836112d8565b9150611dc682611d5f565b604082019050919050565b60006020820190508181036000830152611dea81611dae565b9050919050565b6000606082019050611e066000830186611154565b611e136020830185611154565b611e2060408301846110f8565b949350505050565b7f5665726941756374696f6e546f6b656e466f724574682028676574437572726560008201527f6e745072696365293a206e6f20746f6b656e7320746f2064697374726962757460208201527f6500000000000000000000000000000000000000000000000000000000000000604082015250565b6000611eaa6041836112d8565b9150611eb582611e28565b606082019050919050565b60006020820190508181036000830152611ed981611e9d565b9050919050565b7f5665726941756374696f6e546f6b656e466f724574682028676574437572726560008201527f6e745072696365293a20746f6f206d7563682045544820696e2074686520636f60208201527f6e74726163740000000000000000000000000000000000000000000000000000604082015250565b6000611f626046836112d8565b9150611f6d82611ee0565b606082019050919050565b60006020820190508181036000830152611f9181611f55565b9050919050565b7f4f776e61626c653a206e6577206f776e657220697320746865207a65726f206160008201527f6464726573730000000000000000000000000000000000000000000000000000602082015250565b6000611ff46026836112d8565b9150611fff82611f98565b604082019050919050565b6000602082019050818103600083015261202381611fe7565b9050919050565b7f4f776e61626c653a2063616c6c6572206973206e6f7420746865206f776e6572600082015250565b60006120606020836112d8565b915061206b8261202a565b602082019050919050565b6000602082019050818103600083015261208f81612053565b905091905056fea2646970667358221220f431436cb789886ec71679745d3a7cf0ab5fce20864ef41cde8f3d1e03815bc364736f6c63430008110033",

                  "nonce": 0,

                  "storage": "{}"

                },

                "0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef": {

                  "balance": "0x10a0080800000005",

                  "code": "",

                  "nonce": 0,

                  "storage": "{}"

                }

              }

            },

            "steps": [

              {

                "address": "0x0",

                "blockCoinbase": "0xcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcb",

                "blockDifficulty": "0xa7d7343662e26",

                "blockGasLimit": "0x7d0000",

                "blockNumber": "0x66e393",

                "blockTime": "0x5bfa4639",

                "calldata": "0x88463329",

                "gasLimit": "0x7d000",

                "gasPrice": "0x773594000",

                "input": "0x88463329",

                "name": "unknown",

                "origin": "0xaffeaffeaffeaffeaffeaffeaffeaffeaffeaffe",

                "value": "0x0"

              },

              {

                "address": "0x0",

                "blockCoinbase": "0xcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcb",

                "blockDifficulty": "0xa7d7343662e26",

                "blockGasLimit": "0x7d0000",

                "blockNumber": "0x66e393",

                "blockTime": "0x5bfa4639",

                "calldata": "0x88463329",

                "gasLimit": "0x7d000",

                "gasPrice": "0x773594000",

                "input": "0x88463329",

                "name": "unknown",

                "origin": "0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef",

                "value": "0x0"

              }

            ]

          }

        ]

      },

      "locations": [

        {

          "sourceMap": "2904:1:0"

        }

      ],

      "severity": "High",

      "swcID": "SWC-105",

      "swcTitle": "Unprotected Ether Withdrawal"

    },

    {

      "description": {

        "head": "A call to a user-supplied address is executed.",

        "tail": "An external message call to an address specified by the caller is executed. Note that the callee account might contain arbitrary code and could re-enter any function within this contract. Reentering the contract in an intermediate state may lead to unexpected behaviour. Make sure that no state modifications are executed after this call and/or reentrancy guards are in place."

      },

      "extra": {

        "discoveryTime": 40535990953,

        "testCases": [

          {

            "initialState": {

              "accounts": {

                "0x0": {

                  "balance": "0x10000000000000001",

                  "code": "6080604052600436106100e85760003560e01c8063715018a61161008a578063b1111f1911610059578063b1111f1914610241578063eb91d37e14610258578063ed4460ef14610283578063f2fde38b146102ae576100e8565b8063715018a6146101bd57806388463329146101d45780638da5cb5b146101eb57806399fdb32014610216576100e8565b806345dc13f3116100c657806345dc13f31461015a57806348c54b9d146101645780634bb278f31461017b5780635054f45814610192576100e8565b80630bd8a1d0146100ed5780632a79c611146101185780633ff62dd114610143575b600080fd5b3480156100f957600080fd5b506101026102d7565b60405161010f91906110d3565b60405180910390f35b34801561012457600080fd5b5061012d6102e3565b60405161013a9190611107565b60405180910390f35b34801561014f57600080fd5b5061015861032a565b005b6101626103e8565b005b34801561017057600080fd5b506101796106db565b005b34801561018757600080fd5b50610190610813565b005b34801561019e57600080fd5b506101a761087a565b6040516101b49190611107565b60405180910390f35b3480156101c957600080fd5b506101d261096c565b005b3480156101e057600080fd5b506101e9610980565b005b3480156101f757600080fd5b50610200610c18565b60405161020d9190611163565b60405180910390f35b34801561022257600080fd5b5061022b610c41565b60405161023891906111dd565b60405180910390f35b34801561024d57600080fd5b50610256610c65565b005b34801561026457600080fd5b5061026d610d4b565b60405161027a9190611107565b60405180910390f35b34801561028f57600080fd5b50610298610ebf565b6040516102a59190611107565b60405180910390f35b3480156102ba57600080fd5b506102d560048036038101906102d09190611229565b610ee3565b005b60008060015411905090565b6000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054905090565b610332610f66565b600060035490506003600090557f000000000000000000000000000000000000000000000000000000000000000073ffffffffffffffffffffffffffffffffffffffff1663a9059cbb610383610c18565b836040518363ffffffff1660e01b81526004016103a1929190611256565b6020604051808303816000875af11580156103c0573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906103e491906112ab565b5050565b600060149054906101000a900460ff16610437576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161042e9061135b565b60405180910390fd5b61043f6102d7565b1561047f576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610476906113ed565b60405180910390fd5b600034116104c2576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016104b9906114a5565b60405180910390fd5b6104ca610fe4565b7f0000000000000000000000000000000000000000000000000000000000000000600a6104f79190611627565b7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff61052291906116a1565b1015610563576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161055a9061176a565b60405180910390fd5b34600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020546105ae919061178a565b670de0b6b3a76400007fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff6105e291906116a1565b1015610623576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161061a90611856565b60405180910390fd5b34600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828254610672919061178a565b925050819055503373ffffffffffffffffffffffffffffffffffffffff167f9cff2c0a4d5be51d8a883d8301a9be115ec08ce14e5fe70c21c3f5f507cca33f346106ba610fe4565b6106c2610d4b565b6040516106d193929190611876565b60405180910390a2565b6106e36102d7565b610722576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016107199061191f565b60405180910390fd5b600061072c61087a565b9050600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600090557f000000000000000000000000000000000000000000000000000000000000000073ffffffffffffffffffffffffffffffffffffffff1663a9059cbb33836040518363ffffffff1660e01b81526004016107cc929190611256565b6020604051808303816000875af11580156107eb573d6000803e3d6000fd5b505050506040513d601f19601f8201168201806040525081019061080f91906112ab565b5050565b61081b610f66565b600060149054906101000a900460ff1661086a576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610861906119b1565b60405180910390fd5b610872610fe4565b600181905550565b60006108846102d7565b6108c3576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016108ba90611a69565b60405180910390fd5b6000600154670de0b6b3a7640000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205461091b9190611a89565b61092591906116a1565b9050670de0b6b3a76400007f00000000000000000000000000000000000000000000000000000000000000008261095c9190611a89565b61096691906116a1565b91505090565b610974610f66565b61097e6000610fec565b565b6000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205411610a02576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016109f990611b63565b60405180910390fd5b610a0a6102d7565b15610a975760006003549050610a1e61087a565b81610a29919061178a565b90507f0000000000000000000000000000000000000000000000000000000000000000811115610a8e576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610a8590611c41565b60405180910390fd5b80600381905550505b6000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054905080610ae4610fe4565b1015610b25576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610b1c90611cf9565b60405180910390fd5b60003373ffffffffffffffffffffffffffffffffffffffff1682604051610b4b90611d4a565b60006040518083038185875af1925050503d8060008114610b88576040519150601f19603f3d011682016040523d82523d6000602084013e610b8d565b606091505b5050905080610bd1576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610bc890611dd1565b60405180910390fd5b600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600090555050565b60008060009054906101000a900473ffffffffffffffffffffffffffffffffffffffff16905090565b7f000000000000000000000000000000000000000000000000000000000000000081565b610c6d610f66565b7f000000000000000000000000000000000000000000000000000000000000000073ffffffffffffffffffffffffffffffffffffffff166323b872dd33307f00000000000000000000000000000000000000000000000000000000000000006040518463ffffffff1660e01b8152600401610cea93929190611df1565b6020604051808303816000875af1158015610d09573d6000803e3d6000fd5b505050506040513d601f19601f82011682018060405250810190610d2d91906112ab565b506001600060146101000a81548160ff021916908315150217905550565b6000807f000000000000000000000000000000000000000000000000000000000000000011610daf576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610da690611ec0565b60405180910390fd5b610db7610fe4565b7f0000000000000000000000000000000000000000000000000000000000000000600a610de49190611627565b7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff610e0f91906116a1565b1015610e50576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610e4790611f78565b60405180910390fd5b7f00000000000000000000000000000000000000000000000000000000000000007f0000000000000000000000000000000000000000000000000000000000000000600a610e9e9190611627565b610ea6610fe4565b610eb09190611a89565b610eba91906116a1565b905090565b7f000000000000000000000000000000000000000000000000000000000000000081565b610eeb610f66565b600073ffffffffffffffffffffffffffffffffffffffff168173ffffffffffffffffffffffffffffffffffffffff1603610f5a576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610f519061200a565b60405180910390fd5b610f6381610fec565b50565b610f6e6110b0565b73ffffffffffffffffffffffffffffffffffffffff16610f8c610c18565b73ffffffffffffffffffffffffffffffffffffffff1614610fe2576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610fd990612076565b60405180910390fd5b565b600047905090565b60008060009054906101000a900473ffffffffffffffffffffffffffffffffffffffff169050816000806101000a81548173ffffffffffffffffffffffffffffffffffffffff021916908373ffffffffffffffffffffffffffffffffffffffff1602179055508173ffffffffffffffffffffffffffffffffffffffff168173ffffffffffffffffffffffffffffffffffffffff167f8be0079c531659141344cd1fd0a4f28419497f9722a3daafe3b4186f6b6457e060405160405180910390a35050565b600033905090565b60008115159050919050565b6110cd816110b8565b82525050565b60006020820190506110e860008301846110c4565b92915050565b6000819050919050565b611101816110ee565b82525050565b600060208201905061111c60008301846110f8565b92915050565b600073ffffffffffffffffffffffffffffffffffffffff82169050919050565b600061114d82611122565b9050919050565b61115d81611142565b82525050565b60006020820190506111786000830184611154565b92915050565b6000819050919050565b60006111a361119e61119984611122565b61117e565b611122565b9050919050565b60006111b582611188565b9050919050565b60006111c7826111aa565b9050919050565b6111d7816111bc565b82525050565b60006020820190506111f260008301846111ce565b92915050565b600080fd5b61120681611142565b811461121157600080fd5b50565b600081359050611223816111fd565b92915050565b60006020828403121561123f5761123e6111f8565b5b600061124d84828501611214565b91505092915050565b600060408201905061126b6000830185611154565b61127860208301846110f8565b9392505050565b611288816110b8565b811461129357600080fd5b50565b6000815190506112a58161127f565b92915050565b6000602082840312156112c1576112c06111f8565b5b60006112cf84828501611296565b91505092915050565b600082825260208201905092915050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a2041756374696f6e206e6f742073746172746564000000000000000000602082015250565b60006113456037836112d8565b9150611350826112e9565b604082019050919050565b6000602082019050818103600083015261137481611338565b9050919050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a2041756374696f6e2069732066696e616c697a65640000000000000000602082015250565b60006113d76038836112d8565b91506113e28261137b565b604082019050919050565b60006020820190508181036000830152611406816113ca565b9050919050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a20636f6d6d69746d656e74206d7573742062652067726561746572207460208201527f68616e2030000000000000000000000000000000000000000000000000000000604082015250565b600061148f6045836112d8565b915061149a8261140d565b606082019050919050565b600060208201905081810360008301526114be81611482565b9050919050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052601160045260246000fd5b60008160011c9050919050565b6000808291508390505b600185111561154b57808604811115611527576115266114c5565b5b60018516156115365780820291505b8081029050611544856114f4565b945061150b565b94509492505050565b6000826115645760019050611620565b816115725760009050611620565b81600181146115885760028114611592576115c1565b6001915050611620565b60ff8411156115a4576115a36114c5565b5b8360020a9150848211156115bb576115ba6114c5565b5b50611620565b5060208310610133831016604e8410600b84101617156115f65782820a9050838111156115f1576115f06114c5565b5b611620565b6116038484846001611501565b9250905081840481111561161a576116196114c5565b5b81810290505b9392505050565b6000611632826110ee565b915061163d836110ee565b925061166a7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8484611554565b905092915050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052601260045260246000fd5b60006116ac826110ee565b91506116b7836110ee565b9250826116c7576116c6611672565b5b828204905092915050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a20776f756c6420726573756c7420696e20746f6f206d7563682045544860208201527f20696e207468652061756374696f6e0000000000000000000000000000000000604082015250565b6000611754604f836112d8565b915061175f826116d2565b606082019050919050565b6000602082019050818103600083015261178381611747565b9050919050565b6000611795826110ee565b91506117a0836110ee565b92508282019050808211156117b8576117b76114c5565b5b92915050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a20776f756c6420726573756c74206973206120746f6f206c617267652060208201527f636f6d6d69746d656e7400000000000000000000000000000000000000000000604082015250565b6000611840604a836112d8565b915061184b826117be565b606082019050919050565b6000602082019050818103600083015261186f81611833565b9050919050565b600060608201905061188b60008301866110f8565b61189860208301856110f8565b6118a560408301846110f8565b949350505050565b7f5665726941756374696f6e546f6b656e466f724574682028636c61696d546f6b60008201527f656e73293a2041756374696f6e206e6f742066696e616c697a65642079657400602082015250565b6000611909603f836112d8565b9150611914826118ad565b604082019050919050565b60006020820190508181036000830152611938816118fc565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202866696e616c697a6560008201527f293a2041756374696f6e206e6f74207374617274656400000000000000000000602082015250565b600061199b6036836112d8565b91506119a68261193f565b604082019050919050565b600060208201905081810360008301526119ca8161198e565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202863616c63756c617460008201527f65436c61696d61626c65416d6f756e74293a2061756374696f6e206e6f74206660208201527f696e616c697a6564207965740000000000000000000000000000000000000000604082015250565b6000611a53604c836112d8565b9150611a5e826119d1565b606082019050919050565b60006020820190508181036000830152611a8281611a46565b9050919050565b6000611a94826110ee565b9150611a9f836110ee565b9250828202611aad816110ee565b91508282048414831517611ac457611ac36114c5565b5b5092915050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a206d757374206861766520636f6d6d69746564207360208201527f6f6d652045544800000000000000000000000000000000000000000000000000604082015250565b6000611b4d6047836112d8565b9150611b5882611acb565b606082019050919050565b60006020820190508181036000830152611b7c81611b40565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a20756e636c61696d61626c6520616d6f756e74207760208201527f6f756c64206265206c6172676572207468616e20746f74616c20746f6b656e7360408201527f20746f2064697374726962757465000000000000000000000000000000000000606082015250565b6000611c2b606e836112d8565b9150611c3682611b83565b608082019050919050565b60006020820190508181036000830152611c5a81611c1e565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a20636f6e747261637420646f65736e27742068617660208201527f6520656e6f756768204554480000000000000000000000000000000000000000604082015250565b6000611ce3604c836112d8565b9150611cee82611c61565b606082019050919050565b60006020820190508181036000830152611d1281611cd6565b9050919050565b600081905092915050565b50565b6000611d34600083611d19565b9150611d3f82611d24565b600082019050919050565b6000611d5582611d27565b9150819050919050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a206661696c656420746f2073656e64204554480000602082015250565b6000611dbb603e836112d8565b9150611dc682611d5f565b604082019050919050565b60006020820190508181036000830152611dea81611dae565b9050919050565b6000606082019050611e066000830186611154565b611e136020830185611154565b611e2060408301846110f8565b949350505050565b7f5665726941756374696f6e546f6b656e466f724574682028676574437572726560008201527f6e745072696365293a206e6f20746f6b656e7320746f2064697374726962757460208201527f6500000000000000000000000000000000000000000000000000000000000000604082015250565b6000611eaa6041836112d8565b9150611eb582611e28565b606082019050919050565b60006020820190508181036000830152611ed981611e9d565b9050919050565b7f5665726941756374696f6e546f6b656e466f724574682028676574437572726560008201527f6e745072696365293a20746f6f206d7563682045544820696e2074686520636f60208201527f6e74726163740000000000000000000000000000000000000000000000000000604082015250565b6000611f626046836112d8565b9150611f6d82611ee0565b606082019050919050565b60006020820190508181036000830152611f9181611f55565b9050919050565b7f4f776e61626c653a206e6577206f776e657220697320746865207a65726f206160008201527f6464726573730000000000000000000000000000000000000000000000000000602082015250565b6000611ff46026836112d8565b9150611fff82611f98565b604082019050919050565b6000602082019050818103600083015261202381611fe7565b9050919050565b7f4f776e61626c653a2063616c6c6572206973206e6f7420746865206f776e6572600082015250565b60006120606020836112d8565b915061206b8261202a565b602082019050919050565b6000602082019050818103600083015261208f81612053565b905091905056fea2646970667358221220f431436cb789886ec71679745d3a7cf0ab5fce20864ef41cde8f3d1e03815bc364736f6c63430008110033",

                  "nonce": 0,

                  "storage": "{}"

                },

                "0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef": {

                  "balance": "0x4000",

                  "code": "",

                  "nonce": 0,

                  "storage": "{}"

                }

              }

            },

            "steps": [

              {

                "address": "0x0",

                "blockCoinbase": "0xcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcb",

                "blockDifficulty": "0xa7d7343662e26",

                "blockGasLimit": "0x7d0000",

                "blockNumber": "0x66e393",

                "blockTime": "0x5bfa4639",

                "calldata": "0x88463329",

                "gasLimit": "0x7d000",

                "gasPrice": "0x773594000",

                "input": "0x88463329",

                "name": "unknown",

                "origin": "0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef",

                "value": "0x0"

              }

            ]

          }

        ]

      },

      "locations": [

        {

          "sourceMap": "2904:1:0"

        }

      ],

      "severity": "Low",

      "swcID": "SWC-107",

      "swcTitle": "Reentrancy"

    },

    {

      "description": {

        "head": "Write to persistent state following external call",

        "tail": "The contract account state is accessed after an external call to a user defined address. To prevent reentrancy issues, consider accessing the state only before the call, especially if the callee is untrusted. Alternatively, a reentrancy lock can be used to prevent untrusted callees from re-entering the contract in an intermediate state."

      },

      "extra": {

        "discoveryTime": 40723972082,

        "testCases": [

          {

            "initialState": {

              "accounts": {

                "0x0": {

                  "balance": "0xa3200244000004",

                  "code": "6080604052600436106100e85760003560e01c8063715018a61161008a578063b1111f1911610059578063b1111f1914610241578063eb91d37e14610258578063ed4460ef14610283578063f2fde38b146102ae576100e8565b8063715018a6146101bd57806388463329146101d45780638da5cb5b146101eb57806399fdb32014610216576100e8565b806345dc13f3116100c657806345dc13f31461015a57806348c54b9d146101645780634bb278f31461017b5780635054f45814610192576100e8565b80630bd8a1d0146100ed5780632a79c611146101185780633ff62dd114610143575b600080fd5b3480156100f957600080fd5b506101026102d7565b60405161010f91906110d3565b60405180910390f35b34801561012457600080fd5b5061012d6102e3565b60405161013a9190611107565b60405180910390f35b34801561014f57600080fd5b5061015861032a565b005b6101626103e8565b005b34801561017057600080fd5b506101796106db565b005b34801561018757600080fd5b50610190610813565b005b34801561019e57600080fd5b506101a761087a565b6040516101b49190611107565b60405180910390f35b3480156101c957600080fd5b506101d261096c565b005b3480156101e057600080fd5b506101e9610980565b005b3480156101f757600080fd5b50610200610c18565b60405161020d9190611163565b60405180910390f35b34801561022257600080fd5b5061022b610c41565b60405161023891906111dd565b60405180910390f35b34801561024d57600080fd5b50610256610c65565b005b34801561026457600080fd5b5061026d610d4b565b60405161027a9190611107565b60405180910390f35b34801561028f57600080fd5b50610298610ebf565b6040516102a59190611107565b60405180910390f35b3480156102ba57600080fd5b506102d560048036038101906102d09190611229565b610ee3565b005b60008060015411905090565b6000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054905090565b610332610f66565b600060035490506003600090557f000000000000000000000000000000000000000000000000000000000000000073ffffffffffffffffffffffffffffffffffffffff1663a9059cbb610383610c18565b836040518363ffffffff1660e01b81526004016103a1929190611256565b6020604051808303816000875af11580156103c0573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906103e491906112ab565b5050565b600060149054906101000a900460ff16610437576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161042e9061135b565b60405180910390fd5b61043f6102d7565b1561047f576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610476906113ed565b60405180910390fd5b600034116104c2576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016104b9906114a5565b60405180910390fd5b6104ca610fe4565b7f0000000000000000000000000000000000000000000000000000000000000000600a6104f79190611627565b7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff61052291906116a1565b1015610563576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161055a9061176a565b60405180910390fd5b34600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020546105ae919061178a565b670de0b6b3a76400007fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff6105e291906116a1565b1015610623576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161061a90611856565b60405180910390fd5b34600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828254610672919061178a565b925050819055503373ffffffffffffffffffffffffffffffffffffffff167f9cff2c0a4d5be51d8a883d8301a9be115ec08ce14e5fe70c21c3f5f507cca33f346106ba610fe4565b6106c2610d4b565b6040516106d193929190611876565b60405180910390a2565b6106e36102d7565b610722576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016107199061191f565b60405180910390fd5b600061072c61087a565b9050600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600090557f000000000000000000000000000000000000000000000000000000000000000073ffffffffffffffffffffffffffffffffffffffff1663a9059cbb33836040518363ffffffff1660e01b81526004016107cc929190611256565b6020604051808303816000875af11580156107eb573d6000803e3d6000fd5b505050506040513d601f19601f8201168201806040525081019061080f91906112ab565b5050565b61081b610f66565b600060149054906101000a900460ff1661086a576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610861906119b1565b60405180910390fd5b610872610fe4565b600181905550565b60006108846102d7565b6108c3576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016108ba90611a69565b60405180910390fd5b6000600154670de0b6b3a7640000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205461091b9190611a89565b61092591906116a1565b9050670de0b6b3a76400007f00000000000000000000000000000000000000000000000000000000000000008261095c9190611a89565b61096691906116a1565b91505090565b610974610f66565b61097e6000610fec565b565b6000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205411610a02576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016109f990611b63565b60405180910390fd5b610a0a6102d7565b15610a975760006003549050610a1e61087a565b81610a29919061178a565b90507f0000000000000000000000000000000000000000000000000000000000000000811115610a8e576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610a8590611c41565b60405180910390fd5b80600381905550505b6000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054905080610ae4610fe4565b1015610b25576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610b1c90611cf9565b60405180910390fd5b60003373ffffffffffffffffffffffffffffffffffffffff1682604051610b4b90611d4a565b60006040518083038185875af1925050503d8060008114610b88576040519150601f19603f3d011682016040523d82523d6000602084013e610b8d565b606091505b5050905080610bd1576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610bc890611dd1565b60405180910390fd5b600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600090555050565b60008060009054906101000a900473ffffffffffffffffffffffffffffffffffffffff16905090565b7f000000000000000000000000000000000000000000000000000000000000000081565b610c6d610f66565b7f000000000000000000000000000000000000000000000000000000000000000073ffffffffffffffffffffffffffffffffffffffff166323b872dd33307f00000000000000000000000000000000000000000000000000000000000000006040518463ffffffff1660e01b8152600401610cea93929190611df1565b6020604051808303816000875af1158015610d09573d6000803e3d6000fd5b505050506040513d601f19601f82011682018060405250810190610d2d91906112ab565b506001600060146101000a81548160ff021916908315150217905550565b6000807f000000000000000000000000000000000000000000000000000000000000000011610daf576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610da690611ec0565b60405180910390fd5b610db7610fe4565b7f0000000000000000000000000000000000000000000000000000000000000000600a610de49190611627565b7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff610e0f91906116a1565b1015610e50576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610e4790611f78565b60405180910390fd5b7f00000000000000000000000000000000000000000000000000000000000000007f0000000000000000000000000000000000000000000000000000000000000000600a610e9e9190611627565b610ea6610fe4565b610eb09190611a89565b610eba91906116a1565b905090565b7f000000000000000000000000000000000000000000000000000000000000000081565b610eeb610f66565b600073ffffffffffffffffffffffffffffffffffffffff168173ffffffffffffffffffffffffffffffffffffffff1603610f5a576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610f519061200a565b60405180910390fd5b610f6381610fec565b50565b610f6e6110b0565b73ffffffffffffffffffffffffffffffffffffffff16610f8c610c18565b73ffffffffffffffffffffffffffffffffffffffff1614610fe2576040517f08c379a0000000000000000000000000000000000000000000000000000000008152600401610fd990612076565b60405180910390fd5b565b600047905090565b60008060009054906101000a900473ffffffffffffffffffffffffffffffffffffffff169050816000806101000a81548173ffffffffffffffffffffffffffffffffffffffff021916908373ffffffffffffffffffffffffffffffffffffffff1602179055508173ffffffffffffffffffffffffffffffffffffffff168173ffffffffffffffffffffffffffffffffffffffff167f8be0079c531659141344cd1fd0a4f28419497f9722a3daafe3b4186f6b6457e060405160405180910390a35050565b600033905090565b60008115159050919050565b6110cd816110b8565b82525050565b60006020820190506110e860008301846110c4565b92915050565b6000819050919050565b611101816110ee565b82525050565b600060208201905061111c60008301846110f8565b92915050565b600073ffffffffffffffffffffffffffffffffffffffff82169050919050565b600061114d82611122565b9050919050565b61115d81611142565b82525050565b60006020820190506111786000830184611154565b92915050565b6000819050919050565b60006111a361119e61119984611122565b61117e565b611122565b9050919050565b60006111b582611188565b9050919050565b60006111c7826111aa565b9050919050565b6111d7816111bc565b82525050565b60006020820190506111f260008301846111ce565b92915050565b600080fd5b61120681611142565b811461121157600080fd5b50565b600081359050611223816111fd565b92915050565b60006020828403121561123f5761123e6111f8565b5b600061124d84828501611214565b91505092915050565b600060408201905061126b6000830185611154565b61127860208301846110f8565b9392505050565b611288816110b8565b811461129357600080fd5b50565b6000815190506112a58161127f565b92915050565b6000602082840312156112c1576112c06111f8565b5b60006112cf84828501611296565b91505092915050565b600082825260208201905092915050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a2041756374696f6e206e6f742073746172746564000000000000000000602082015250565b60006113456037836112d8565b9150611350826112e9565b604082019050919050565b6000602082019050818103600083015261137481611338565b9050919050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a2041756374696f6e2069732066696e616c697a65640000000000000000602082015250565b60006113d76038836112d8565b91506113e28261137b565b604082019050919050565b60006020820190508181036000830152611406816113ca565b9050919050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a20636f6d6d69746d656e74206d7573742062652067726561746572207460208201527f68616e2030000000000000000000000000000000000000000000000000000000604082015250565b600061148f6045836112d8565b915061149a8261140d565b606082019050919050565b600060208201905081810360008301526114be81611482565b9050919050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052601160045260246000fd5b60008160011c9050919050565b6000808291508390505b600185111561154b57808604811115611527576115266114c5565b5b60018516156115365780820291505b8081029050611544856114f4565b945061150b565b94509492505050565b6000826115645760019050611620565b816115725760009050611620565b81600181146115885760028114611592576115c1565b6001915050611620565b60ff8411156115a4576115a36114c5565b5b8360020a9150848211156115bb576115ba6114c5565b5b50611620565b5060208310610133831016604e8410600b84101617156115f65782820a9050838111156115f1576115f06114c5565b5b611620565b6116038484846001611501565b9250905081840481111561161a576116196114c5565b5b81810290505b9392505050565b6000611632826110ee565b915061163d836110ee565b925061166a7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8484611554565b905092915050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052601260045260246000fd5b60006116ac826110ee565b91506116b7836110ee565b9250826116c7576116c6611672565b5b828204905092915050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a20776f756c6420726573756c7420696e20746f6f206d7563682045544860208201527f20696e207468652061756374696f6e0000000000000000000000000000000000604082015250565b6000611754604f836112d8565b915061175f826116d2565b606082019050919050565b6000602082019050818103600083015261178381611747565b9050919050565b6000611795826110ee565b91506117a0836110ee565b92508282019050808211156117b8576117b76114c5565b5b92915050565b7f5665726941756374696f6e546f6b656e466f724574682028636f6d6d6974457460008201527f68293a20776f756c6420726573756c74206973206120746f6f206c617267652060208201527f636f6d6d69746d656e7400000000000000000000000000000000000000000000604082015250565b6000611840604a836112d8565b915061184b826117be565b606082019050919050565b6000602082019050818103600083015261186f81611833565b9050919050565b600060608201905061188b60008301866110f8565b61189860208301856110f8565b6118a560408301846110f8565b949350505050565b7f5665726941756374696f6e546f6b656e466f724574682028636c61696d546f6b60008201527f656e73293a2041756374696f6e206e6f742066696e616c697a65642079657400602082015250565b6000611909603f836112d8565b9150611914826118ad565b604082019050919050565b60006020820190508181036000830152611938816118fc565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202866696e616c697a6560008201527f293a2041756374696f6e206e6f74207374617274656400000000000000000000602082015250565b600061199b6036836112d8565b91506119a68261193f565b604082019050919050565b600060208201905081810360008301526119ca8161198e565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202863616c63756c617460008201527f65436c61696d61626c65416d6f756e74293a2061756374696f6e206e6f74206660208201527f696e616c697a6564207965740000000000000000000000000000000000000000604082015250565b6000611a53604c836112d8565b9150611a5e826119d1565b606082019050919050565b60006020820190508181036000830152611a8281611a46565b9050919050565b6000611a94826110ee565b9150611a9f836110ee565b9250828202611aad816110ee565b91508282048414831517611ac457611ac36114c5565b5b5092915050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a206d757374206861766520636f6d6d69746564207360208201527f6f6d652045544800000000000000000000000000000000000000000000000000604082015250565b6000611b4d6047836112d8565b9150611b5882611acb565b606082019050919050565b60006020820190508181036000830152611b7c81611b40565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a20756e636c61696d61626c6520616d6f756e74207760208201527f6f756c64206265206c6172676572207468616e20746f74616c20746f6b656e7360408201527f20746f2064697374726962757465000000000000000000000000000000000000606082015250565b6000611c2b606e836112d8565b9150611c3682611b83565b608082019050919050565b60006020820190508181036000830152611c5a81611c1e565b9050919050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a20636f6e747261637420646f65736e27742068617660208201527f6520656e6f756768204554480000000000000000000000000000000000000000604082015250565b6000611ce3604c836112d8565b9150611cee82611c61565b606082019050919050565b60006020820190508181036000830152611d1281611cd6565b9050919050565b600081905092915050565b50565b6000611d34600083611d19565b9150611d3f82611d24565b600082019050919050565b6000611d5582611d27565b9150819050919050565b7f5665726941756374696f6e546f6b656e466f72457468202872657369676e467260008201527f6f6d41756374696f6e293a206661696c656420746f2073656e64204554480000602082015250565b6000611dbb603e836112d8565b9150611dc682611d5f565b604082019050919050565b60006020820190508181036000830152611dea81611dae565b9050919050565b6000606082019050611e066000830186611154565b611e136020830185611154565b611e2060408301846110f8565b949350505050565b7f5665726941756374696f6e546f6b656e466f724574682028676574437572726560008201527f6e745072696365293a206e6f20746f6b656e7320746f2064697374726962757460208201527f6500000000000000000000000000000000000000000000000000000000000000604082015250565b6000611eaa6041836112d8565b9150611eb582611e28565b606082019050919050565b60006020820190508181036000830152611ed981611e9d565b9050919050565b7f5665726941756374696f6e546f6b656e466f724574682028676574437572726560008201527f6e745072696365293a20746f6f206d7563682045544820696e2074686520636f60208201527f6e74726163740000000000000000000000000000000000000000000000000000604082015250565b6000611f626046836112d8565b9150611f6d82611ee0565b606082019050919050565b60006020820190508181036000830152611f9181611f55565b9050919050565b7f4f776e61626c653a206e6577206f776e657220697320746865207a65726f206160008201527f6464726573730000000000000000000000000000000000000000000000000000602082015250565b6000611ff46026836112d8565b9150611fff82611f98565b604082019050919050565b6000602082019050818103600083015261202381611fe7565b9050919050565b7f4f776e61626c653a2063616c6c6572206973206e6f7420746865206f776e6572600082015250565b60006120606020836112d8565b915061206b8261202a565b602082019050919050565b6000602082019050818103600083015261208f81612053565b905091905056fea2646970667358221220f431436cb789886ec71679745d3a7cf0ab5fce20864ef41cde8f3d1e03815bc364736f6c63430008110033",

                  "nonce": 0,

                  "storage": "{}"

                },

                "0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef": {

                  "balance": "0x4",

                  "code": "",

                  "nonce": 0,

                  "storage": "{}"

                }

              }

            },

            "steps": [

              {

                "address": "0x0",

                "blockCoinbase": "0xcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcbcb",

                "blockDifficulty": "0xa7d7343662e26",

                "blockGasLimit": "0x7d0000",

                "blockNumber": "0x66e393",

                "blockTime": "0x5bfa4639",

                "calldata": "0x88463329",

                "gasLimit": "0x7d000",

                "gasPrice": "0x773594000",

                "input": "0x88463329",

                "name": "unknown",

                "origin": "0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef",

                "value": "0x0"

              }

            ]

          }

        ]

      },

      "locations": [

        {

          "sourceMap": "3092:1:0"

        }

      ],

      "severity": "Medium",

      "swcID": "SWC-107",

      "swcTitle": "Reentrancy"

    }

  ],

  "meta": {

    "mythril_execution_info": {

      "analysis_duration": 829335309029

    }

  },

  "sourceFormat": "evm-byzantium-bytecode",

  "sourceList": [

    "0x916cb9a2b1de5ee216783d386e83ba7cf6574804e5735036868132e0f2592571"

  ],

  "sourceType": "raw-bytecode"

}

```

