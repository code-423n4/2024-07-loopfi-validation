## Description
Protocol integrates the `PendleLpOracle`, according to the Pendle finance documentation to integrate the oracle `By default, markets' oracles are un-initialized. You can check the oracle's status & initialize it if necessary using getOracleState on the PendlePYLpOracle contract.` and provides the way to initialize the oracle. `PendleLPOracle::_validatePtOracle()` function in the protocol attempts to do this, but does not follow the right practices as if the `increaseCardinalityRequired` and `!oldestObservationSatisfied`, it needs to wait for the twap duration for data population and call `IPMarket(market).increaseObservationCardinalityNext(cardinalityRequired)` but instead it performs the direct check `if(!increaseCardinalityRequired && oldestObservationSatisfied) return true`.

For more info, documentation link: <https://docs.pendle.finance/Developers/Oracles/HowToIntegratePtAndLpOracle>

## Impact
protocol will not maintain the correct functionality which can lead to issues e.g the transaction will revert upon returning false.
## Proof of Concept
https://github.com/code-423n4/2024-07-loopfi/blob/57871f64bdea450c1f04c9a53dc1a78223719164/src/oracle/PendleLPOracle.sol#L137-L150
## Recommended Mitigation
```diff
function _validatePtOracle() internal view returns (bool isValid) {
        try ptOracle.getOracleState(address(market), twapWindow) returns (
            bool increaseCardinalityRequired,
            uint16,
            bool oldestObservationSatisfied
        ) {
+           if (increaseCardinalityRequired) {
+             //call 
          
      +//IPMarket(market).increaseObservationCardinalityNext(cardinalityRequired) 
+             //and wait for atleast the twapDuration, 
+             //to allow data population
+            }

+            if (!oldestObservationSatisfied) {
+              //it is necessary to wait for twap duration for data population
+            }

            if(!increaseCardinalityRequired && oldestObservationSatisfied) return true; 
        } 
        catch {
            // return default value on failure
        }
       
    }
}

```