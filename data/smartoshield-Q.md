## [QA-1] Inconsistent import path for `RecoverERC20.sol`

### 1- Description
The `MultiFeeDistribution.sol` file imports the `RecoverERC20.sol` file from the `src/reward/libraries/` directory, which is out of scope for the audit.

Conversely, the `ChefIncentivesController.sol` file correctly imports the in-scope `RecoverERC20.sol` file located in the `src/reward/` directory.

Although an out-of-scope file is involved, **the root cause lies within an in-scope file** which is importing this out-of-scope contract.

If the `src/reward/libraries/RecoverERC20.sol` file is deleted before deployment, the `MultiFeeDistribution.sol` file fails to compile due to the missing file.

Given that both `RecoverERC20.sol` files are identical (differing only in a comment line 8, @author Radiant Devs vs @author Loopfi), it’s reasonable to assume that the `src/reward/libraries/RecoverERC20.sol` file may be deleted before production.

### 2- Impact
This issue does not introduce any security vulnerabilities, but **it disrupts the deployment process**. The problem is automatically caught during the compilation phase, requiring manual intervention to correct the import path.
Additionally, maintaining several identical files in different locations may lead to unnecessary confusion and maintenance challenges.

### 3- Recommendation
**Update import statements**: Ensure that the `MultiFeeDistribution.sol` file imports the in-scope `RecoverERC20.sol` file from the `src/reward/` directory.

`MultiFeeDistribution.sol - Line 11`
```diff
-   import {RecoverERC20} from "./libraries/RecoverERC20.sol";
+   import {RecoverERC20} from "./RecoverERC20.sol";
```

### 4- Proof of Concept
The issue was reproduced by deleting the `src/reward/libraries` folder and attempting to compile the project with `forge compile`. This action led to a compilation failure.
Updating the import path as recommended allowed the project to compile successfully, confirming that the issue was resolved.