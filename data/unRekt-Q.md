## Title [Low]: `ChainlinkOracle::_fetchAndValidate` function doesn't check for `min/max` price.

## Summary :
`ChainlinkOracle::_fetchAndValidate` function do not check for staleness of the fetched data

## Vulnerability details:
The `ChainlinkOracle::_fetchAndValidate` function is using the aggregator v3 to get/call the `latestRoundData`.
```js
    /// @notice Fetches and validates the latest price from Chainlink
    /// @return isValid Whether the price is valid based on the value range and staleness
    /// @return price Asset price [WAD]
    function _fetchAndValidate(address token) internal view returns (bool isValid, uint256 price) {
        Oracle memory oracle = oracles[token];
        //@audit not checking if value if between min and max answer
        try AggregatorV3Interface(oracle.aggregator).latestRoundData() returns (
            uint80 /*roundId*/,
            int256 answer,
            uint256 /*startedAt*/,
            uint256 updatedAt,
            uint80 /*answeredInRound*/
        ) {
            isValid = (answer > 0 && block.timestamp - updatedAt <= oracle.stalePeriod);
            return (isValid, wdiv(uint256(answer), oracle.aggregatorScale));
        } catch {//@audit catch block is empty
            // return the default values (false, 0) on failure
        }
    }
```
[link to code](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/ChainlinkOracle.sol#L93-L110)

The function should check for the min and max amount return to prevent some case happen, something like this:

https://solodit.xyz/issues/missing-checks-for-chainlink-oracle-spearbit-connext-pdf
https://solodit.xyz/issues/m-16-chainlinkadapteroracle-will-return-the-wrong-price-for-asset-if-underlying-aggregator-hits-minanswer-sherlock-blueberry-blueberry-git


Also as we can see above from the function code, the `catch` block is empty. In case the `try` block throws any exception there is nothing to handle it. Which can lead to silent failures and unwanted behavior

## Impact
If case like luna happen then the oracle will return the minimum price and not the crashed price. Function can show unwanted or unexpected behavior in case `try` block throws an exception.

## Recommended mitigation
Some check like this can be added to avoid returning of the min price or the max price in case of the price crashes.
```js
          require(answer < _maxPrice, "Upper price bound breached");
          require(answer > _minPrice, "Lower price bound breached");
```
And appropriate logic should be implemented in `catch` block.

---

## Title [Info/NC] Empty `catch` block in `PendleLPOracle::_validatePtOracle`

## Summary : 
`catch` block is empty

## Vulnerability details :
As we can see in the function the function implements `try/catch` block buy the catch block is empty.
```js
    /// @notice Validates the PT oracle
    /// @return isValid Whether the PT oracle is valid for this market and twap window
    function _validatePtOracle() internal view returns (bool isValid) {
        try ptOracle.getOracleState(address(market), twapWindow) returns (
            bool increaseCardinalityRequired,
            uint16,
            bool oldestObservationSatisfied
        ) {
            if(!increaseCardinalityRequired && oldestObservationSatisfied) return true; 
        } 
        catch {//@audit catch block empty
            // return default value on failure
        }
       
    }
```
[link to code](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/PendleLPOracle.sol#L135-L149)

## Impact
Function can show unwanted or unexpected behavior in case `try` block throws an exception.

## Tools used
Manual review

## Recommended mitigation
Implement required logic in the `catch` block.

---

## Title [Info/NC]: `Flashlender::creditFlashLoan` and `Flashlender::flashLoan` function has hardcoded return values as `true` 

## Summary :
`Flashlender::creditFlashLoan` and `Flashlender::flashLoan` function has hardcoded return values as `true` 

## Vulnerability details:

`Flashlender::creditFlashLoan` function :
[link to code](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L117-L137)

```js
    function creditFlashLoan(
        ICreditFlashBorrower receiver,
        uint256 amount,
        bytes calldata data
    ) external override nonReentrant returns (bool) {
        uint256 fee = wmul(amount, protocolFee);
        uint256 total = amount + fee;

        pool.lendCreditAccount(amount, address(receiver));

        emit CreditFlashLoan(address(receiver), amount, fee);

        if (receiver.onCreditFlashLoan(msg.sender, amount, fee, data) != CALLBACK_SUCCESS_CREDIT)
            revert Flash__creditFlashLoan_callbackFailed();

        // reverts if not enough Stablecoin have been send back
        underlyingToken.transferFrom(address(receiver), address(pool), total);
        pool.repayCreditAccount(total - fee, fee, 0);

        return true;//@audit hardcoded returns true
    }
```

`Flashlender::flashLoan` function :
[link to code](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/Flashlender.sol#L87-L109)

```js
    function flashLoan(
        IERC3156FlashBorrower receiver,
        address token,
        uint256 amount,
        bytes calldata data
    ) external override nonReentrant returns (bool) {
        if (token != address(underlyingToken)) revert Flash__flashLoan_unsupportedToken();
        uint256 fee = wmul(amount, protocolFee);
        uint256 total = amount + fee;

        pool.lendCreditAccount(amount, address(receiver));

        emit FlashLoan(address(receiver), token, amount, fee);

        if (receiver.onFlashLoan(msg.sender, token, amount, fee, data) != CALLBACK_SUCCESS)
            revert Flash__flashLoan_callbackFailed();

        // reverts if not enough Stablecoin have been send back
        underlyingToken.transferFrom(address(receiver), address(pool), total);//@audit using transferFrom
        pool.repayCreditAccount(total - fee, fee, 0);

        return true;//@audit hardcoded returns true
    }
```
As we can see above both the function is supposed to return a `boolean`, which is based on the logic that is implemented in the function body. But in both the function the `return` value is hardcoded as `true`

## Impact
In case of failure, the return value would still be `true`

## Tools used
Manual review

## Recommended mitigation
Return value should be based on the logic implemented, and not hardcoded.

---

## Title [Info/NC]: `PoolV3::_convertToShares` and `PoolV3::_convertToAssets` functions return the input values without performing any computation

## Summary
 `PoolV3::_convertToShares` and `PoolV3::_convertToAssets` functions return the input values

## Vulnerability details
`PoolV3::_convertToShares` function :
[link to code](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L427-L432)

```js
    /// @dev Internal conversion function (from assets to shares) with support for rounding direction
    /// @dev Pool is not vulnerable to the inflation attack, so the simplified implementation w/o virtual shares is used
    function _convertToShares(uint256 assets) internal pure returns (uint256 shares) {
        // uint256 supply = totalSupply();
        return assets; //(assets == 0 || supply == 0) ? assets : assets.mulDiv(supply, totalAssets(), rounding);
    }
```
`PoolV3::_convertToAssets` function :
[link to code](https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/PoolV3.sol#L434-L439)

```js
    /// @dev Internal conversion function (from shares to assets) with support for rounding direction
    /// @dev Pool is not vulnerable to the inflation attack, so the simplified implementation w/o virtual shares is used
    function _convertToAssets(uint256 shares) internal pure returns (uint256 assets) {
        //uint256 supply = totalSupply();
        return shares; //(supply == 0) ? shares : shares.mulDiv(totalAssets(), supply, rounding);
    }
```
From the above function we can see that it just returns the input value without performing any kind of computation or logic. 
The logic which might have been performed is commented.

From the comments above the function:

`PoolV3::_convertToShares` - 
```
    /// @dev Internal conversion function (from assets to shares) with support for rounding direction
```
 `PoolV3::_convertToAssets` -
```
    /// @dev Internal conversion function (from shares to assets) with support for rounding direction
```
Both the functions are supposed to convert a particular data to another, but the functions just return the input as it is.

## Impact
Wrong data will be returned

## Tools used
Manual review

## Recommended mitigation
Implement the required logic to calculate or convert the expected data required from the function.

