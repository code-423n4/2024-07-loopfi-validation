## Title
 Unauthorised call of the maxFlashLoan function in the Flashlender contract

## Location
```url
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L64-L68
```

## Code
```sol
    function maxFlashLoan(address token) external view override returns (uint256 max) {
        if (token == address(underlyingToken)) {
            max = pool.creditManagerBorrowable(address(this));
        }
    }
```

## Impact
Detailed description of the impact of this finding.

The maxFlashLoan function can be accessed by unauthorised users. 

This function reveals the maximum flash loan amount available for a given token without any restrictions. 

While this does not directly lead to financial loss, it exposes sensitive internal state information that could potentially be used in planning attacks or identifying liquidity levels.

The maxFlashLoan function allows any external caller to query the maximum amount available for a flash loan for a specific token. 

While this function only provides information and does not modify state or execute financial operations, it could disclose sensitive details about the liquidity available in the protocol, which might be leveraged by malicious actors to optimise the timing and magnitude of attacks or arbitrage opportunities.

The primary impact of this vulnerability is informational exposure, which can lead to:

Reconnaissance: 
Malicious actors gaining insights into the protocol’s available liquidity, aiding in planning attacks.

Arbitrage Exploitation: 
Potential for informed arbitrage strategies by observing the liquidity levels of specific tokens.
## Proof of Concept
Provide direct links to all referenced code in GitHub. Add screenshots, logs, or any other relevant proof that illustrates the concept.

The following test demonstrates how an unauthorised entity can call maxFlashLoan to retrieve liquidity information:
```sol
function test_max_flash_loan() public {
        address addr = address(0x1);
        uint256 maxAvailable = underlyingToken.balanceOf(address(liquidityPool));
        vm.prank(address(0xbEEF));
        flashlender.maxFlashLoan(address(underlyingToken));
    }
```

In this proof of concept, the address 0xbEEF is able to retrieve the maximum flash loan amount available for underlyingToken, illustrating the lack of access restrictions on this informational function.

***Step 1***
Create file & path: ```src/test/unit/Flashlender4Debo.t.sol```

***Step 2***
Copy the code below into the file.
```sol
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.19;

import {Test} from "forge-std/Test.sol";

import {TestBase, ERC20PresetMinterPauser, PoolV3, IERC20, TransparentUpgradeableProxy, ProxyAdmin, IPoolV3} from "../TestBase.sol";

import {WAD} from "../../utils/Math.sol";

import {IFlashlender, FlashLoanReceiverBase, IERC3156FlashBorrower} from "../../interfaces/IFlashlender.sol";
import {IPermission} from "../../interfaces/IPermission.sol";

import {CDPVault} from "../../CDPVault.sol";
import {Flashlender} from "../../Flashlender.sol";

abstract contract TestReceiver is FlashLoanReceiverBase {
    constructor(address flash) FlashLoanReceiverBase(flash) {
        // IPoolV3 pool = IFlashlender(flash).pool();
    }

    function _mintFee(uint256 amount) internal {
        if (amount > 0) {
            ERC20PresetMinterPauser token = ERC20PresetMinterPauser(address(flashlender.underlyingToken()));
            token.mint(address(this), amount);
        }
    }
}

contract TestImmediatePaybackReceiver is TestReceiver {
    constructor(address flash) TestReceiver(flash) {}

    function onFlashLoan(
        address,
        address,
        uint256 amount_,
        uint256 fee_,
        bytes calldata
    ) external override returns (bytes32) {
        _mintFee(fee_);
        // Just pay back the original amount
        approvePayback(amount_ + fee_);

        return CALLBACK_SUCCESS;
    }
    function onCreditFlashLoan(
        address,
        uint256,
        uint256 fee_,
        bytes calldata
    ) external override returns (bytes32) {
        _mintFee(fee_);
        return CALLBACK_SUCCESS_CREDIT;
    }
}

contract TestReentrancyReceiver is TestReceiver {
    TestImmediatePaybackReceiver public immediatePaybackReceiver;

    constructor(address flash) TestReceiver(flash) {
        immediatePaybackReceiver = new TestImmediatePaybackReceiver(flash);
    }

    function onFlashLoan(
        address,
        address token_,
        uint256 amount_,
        uint256 fee_,
        bytes calldata data_
    ) external override returns (bytes32) {
        flashlender.flashLoan(immediatePaybackReceiver, token_, amount_ + fee_, data_);

        approvePayback(amount_ + fee_);

        return CALLBACK_SUCCESS;
    }
    function onCreditFlashLoan(
        address,
        uint256 amount_,
        uint256 fee_,
        bytes calldata data_
    ) external override returns (bytes32) {
        flashlender.creditFlashLoan(immediatePaybackReceiver, amount_ + fee_, data_);

        return CALLBACK_SUCCESS_CREDIT;
    }
}

contract TestDEXTradeReceiver is TestReceiver {
    IPoolV3 public pool;
    ERC20PresetMinterPauser public underlyingToken;
    ERC20PresetMinterPauser public token;
    CDPVault public vaultA;

    constructor(address flash, address token_, address vaultA_) TestReceiver(flash) {
        pool = IFlashlender(flash).pool();
        underlyingToken = ERC20PresetMinterPauser(address(IFlashlender(flash).underlyingToken()));
        token = ERC20PresetMinterPauser(token_);
        vaultA = CDPVault(vaultA_);
    }

    function onFlashLoan(
        address,
        address,
        uint256 amount_,
        uint256 fee_,
        bytes calldata
    ) external override returns (bytes32) {
        address me = address(this);
        uint256 totalDebt = amount_ + fee_;
        uint256 tokenAmount = totalDebt * 3;

        // Perform a "trade"
        underlyingToken.transfer(address(0x1), amount_);
        token.mint(me, tokenAmount);

        // Create a position and borrow underlying tokens
        token.approve(address(vaultA), type(uint256).max);
        vaultA.modifyCollateralAndDebt(me, me, me, int256(tokenAmount), int256(totalDebt));

        approvePayback(amount_ + fee_);

        return CALLBACK_SUCCESS;
    }

    function onCreditFlashLoan(
        address,
        uint256,
        uint256,
        bytes calldata
    ) external override pure returns (bytes32) {
        return CALLBACK_SUCCESS_CREDIT;
    }
}

contract TestBadReturn is TestReceiver {
    bytes32 public constant BAD_HASH = keccak256("my bad hash");

    constructor(address flash) TestReceiver(flash) {}

    function onFlashLoan(
        address,
        address,
        uint256 amount_,
        uint256 fee_,
        bytes calldata
    ) external override returns (bytes32) {
        _mintFee(fee_);
        approvePayback(amount_ + fee_);

        return BAD_HASH;
    }
    function onCreditFlashLoan(
        address,
        uint256,
        uint256 fee_,
        bytes calldata
    ) external override returns (bytes32) {
        _mintFee(fee_);
        return BAD_HASH;
    }
}

contract TestNoFeePaybackReceiver is TestReceiver {
    constructor(address flash) TestReceiver(flash) {}

    function onFlashLoan(
        address,
        address,
        uint256 amount_,
        uint256,
        bytes calldata
    ) external override returns (bytes32) {
        // Just pay back the original amount w/o fee
        approvePayback(amount_);
        return CALLBACK_SUCCESS;
    }
    function onCreditFlashLoan(
        address,
        uint256,
        uint256,
        bytes calldata
    ) external override pure returns (bytes32) {
        return CALLBACK_SUCCESS_CREDIT;
    }
}

contract TestNoCallbacks {}

contract Flashlender4DeboTest is TestBase {
    address public me;

    CDPVault public vault;

    TestImmediatePaybackReceiver public immediatePaybackReceiver;
    TestImmediatePaybackReceiver public immediatePaybackReceiverOne; // 1% fee
    TestImmediatePaybackReceiver public immediatePaybackReceiverFive; // 5% fee

    TestNoFeePaybackReceiver public noFeePaybackReceiver; // 1% fee

    TestReentrancyReceiver public reentrancyReceiver;
    TestDEXTradeReceiver public dexTradeReceiver;
    TestBadReturn public badReturn;
    TestNoCallbacks public noCallbacks;

    Flashlender flashlenderOne; // w/ 1% fee
    Flashlender flashlenderFive; // w/ 5% fee

    // override cdm to manually mint fees and flashlender with fees
    function createCore() internal override {
        super.createCore();
        flashlenderOne = new Flashlender(IPoolV3(address(liquidityPool)), 1e16); // 1% fee
        flashlenderFive = new Flashlender(IPoolV3(address(liquidityPool)), 5e16); // 5% fee
        setGlobalDebtCeiling(5_000_000 ether);

        liquidityPool.setCreditManagerDebtLimit(address(flashlenderOne), type(uint256).max);
        liquidityPool.setCreditManagerDebtLimit(address(flashlenderFive), type(uint256).max);
    }

    function setUp() public override {
        super.setUp();
        me = address(this);
        underlyingToken = mockWETH;

        // set up vault
        vault = createCDPVault(
            token,
            5_000_000 ether, // debt ceiling
            0, // debt floor
            1.25 ether, // liquidation ratio
            1.0 ether, // liquidation penalty
            1.05 ether // liquidation discount
        );
        createGaugeAndSetGauge(address(vault));
        // deploy receivers
        immediatePaybackReceiver = new TestImmediatePaybackReceiver(address(flashlender));
        immediatePaybackReceiverOne = new TestImmediatePaybackReceiver(address(flashlenderOne));
        immediatePaybackReceiverFive = new TestImmediatePaybackReceiver(address(flashlenderFive));

        bytes32 minterRole = keccak256("MINTER_ROLE");
        underlyingToken.grantRole(minterRole, address(immediatePaybackReceiver));
        underlyingToken.grantRole(minterRole, address(immediatePaybackReceiverOne));
        underlyingToken.grantRole(minterRole, address(immediatePaybackReceiverFive));

        noFeePaybackReceiver = new TestNoFeePaybackReceiver(address(flashlenderOne));

        reentrancyReceiver = new TestReentrancyReceiver(address(flashlender));
        dexTradeReceiver = new TestDEXTradeReceiver(address(flashlender), address(token), address(vault));
        badReturn = new TestBadReturn(address(flashlender));
        noCallbacks = new TestNoCallbacks();
    }

    function test_max_flash_loan() public {
        address addr = address(0x1);
        uint256 maxAvailable = underlyingToken.balanceOf(address(liquidityPool));
        vm.prank(address(0xbEEF));
        flashlender.maxFlashLoan(address(underlyingToken));
    }

}
```

***Step 3***
Go to terminal and CD into root folder: 2024-07-loopfi>> and then run ```forge test -vvvv --match-contract Flashlender4DeboTest --ffi```

***Step 4***
The results are as follows.
```log
forge test -vvvv --match-contract Flashlender4DeboTest --ffi
[⠢] Compiling...
[⠒] Compiling 2 files with Solc 0.8.19
[⠢] Solc 0.8.19 finished in 4.00s
Compiler run successful with warnings:
Warning (2072): Unused local variable.
   --> src/test/unit/Flashlender4Debo.t.sol:259:9:
    |
259 |         address addr = address(0x1);
    |         ^^^^^^^^^^^^

Warning (2072): Unused local variable.
   --> src/test/unit/Flashlender4Debo.t.sol:260:9:
    |
260 |         uint256 maxAvailable = underlyingToken.balanceOf(address(liquidityPool));
    |         ^^^^^^^^^^^^^^^^^^^^


Ran 1 test for src/test/unit/Flashlender4Debo.t.sol:Flashlender4DeboTest
[PASS] test_max_flash_loan() (gas: 38427)
Traces:
  [38427] Flashlender4DeboTest::test_max_flash_loan()
    ├─ [2560] UnderlyingToken::balanceOf(0x50D1DED96CFA10063C9eb319A101E56BA6AB1047) [staticcall]
    │   └─ ← [Return] 1000000000000000000000000 [1e24]
    ├─ [0] VM::prank(0x000000000000000000000000000000000000bEEF)
    │   └─ ← [Return] 
    ├─ [20333] 0x94356885bAab66B09250CE0bac746e445Bc4692F::maxFlashLoan(UnderlyingToken: [0x4D6321dC7A274992769ec84B5e94F642A1B566F9]) [staticcall]
    │   ├─ [16989] 0x50D1DED96CFA10063C9eb319A101E56BA6AB1047::creditManagerBorrowable(0x94356885bAab66B09250CE0bac746e445Bc4692F) [staticcall]
    │   │   ├─ [560] UnderlyingToken::balanceOf(0x50D1DED96CFA10063C9eb319A101E56BA6AB1047) [staticcall]
    │   │   │   └─ ← [Return] 1000000000000000000000000 [1e24]
    │   │   ├─ [405] 0xaE79994bE4fDfd2816b951a2392ba6acDE3e9A61::availableToBorrow(1000000000000000000000000 [1e24], 1000000000000000000000000 [1e24]) [staticcall]
    │   │   │   └─ ← [Return] 1000000000000000000000000 [1e24]
    │   │   └─ ← [Return] 1000000000000000000000000 [1e24]
    │   └─ ← [Return] 1000000000000000000000000 [1e24]
    └─ ← [Stop] 

Suite result: ok. 1 passed; 0 failed; 0 skipped; finished in 3.71ms (292.21µs CPU time)

Ran 1 test suite in 141.53ms (3.71ms CPU time): 1 tests passed, 0 failed, 0 skipped (1 total tests)
```
## Tools Used
Manual review & foundry test.
## Recommended Mitigation Steps

To mitigate the potential for misuse, consider adding access control to limit who can call the maxFlashLoan function. 

While this function does not directly lead to financial transactions, limiting access to trusted entities can prevent unnecessary exposure of internal state information. 

Implementing OpenZeppelin’s AccessControl could provide a simple mechanism for managing this access:
```sol
    function maxFlashLoan(address token) external view override onlyRole(FLASHLOAN_INFO_ROLE) returns (uint256 max) {
```