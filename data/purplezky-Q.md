In /src/oracle/ChainlinkOracle.sol in the function _fetchAndValidate there is a call to latestRoundData Chainlink Oracle.

However it seems round completeness is ignored completely.
You should add a check for round completeness
``isValid = (answer > 0 
           && block.timestamp - updatedAt <= oracle.stalePeriod
           && answeredInRound >= roundId);
``

If there is a problem with chainlink starting a new round and finding consensus on the new value for the oracle (e.g. chainlink nodes abandon the oracle, chain congestion, vulnerability/attacks on the chainlink system) consumers of this contract may continue using outdated data (if oracles are unable to submit no new round is started).