# Auction Smart Contract audit report.

## Summary

## Finding

## Security issues

### 1. Floating Pragma
   Severity: 


   Description

   Detailed description can be found [here](https://swcregistry.io/docs/SWC-103)


   Recommendation

---------------------

### 2. Divide before multiply
Severity: Medium


Description:

 In Solidity integer division might truncate data. Unless the limit of a smaller type makes this problematic, rearranging math such that multiplication comes before division is typically a smart idea.

Detailed description can be found [here](https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply)

      In function claimTokens(){
        //
        //
      uint256 amountOfTokens = supplyToDistribute * userDepositedUSD / totalRaisedUSD;
      //
      //
      }


Recommendation

Consider ordering multiplication before division

------------------------------

### 3. Reentrancy vulnerabilities
Severity: high


Description

Detailed description can be found [here](https://swcregistry.io/docs/SWC-107)

     function refundAll(){
      //
      //
      payable(addressList[i]).transfer(user.bnbAmount);
      user.bnbAmount = 0;
      //
      }
        



Recommendation

Update state before transfer

-------------------------
### 4. Unchecked transfer
Severity: High 


Description:

The return value of an external transfer/transferFrom call is not checked

Detailed description can be found [here](https://github.com/crytic/slither/wiki/Detector-Documentation#unchecked-transfer)

   
      
      

Recommendation
  
  Several tokens do not revert in case of failure and return false. If one of these tokens is used in MyBank, deposit will not revert if the transfer fails, and an attacker can call deposit for free.


---------------------------

### 5. Boolean equality
Severity: Informational


Description

Boolean constants can be used directly and do not need to be compare to true or false.

Detailed description can be found [here](https://github.com/crytic/slither/wiki/Detector-Documentation#boolean-equality)


     function checkConditions(uint256 _amount) internal view {
            //
            require(isClosed == false, "funding is reached");
        }

Recommendation

Remove the equality to the boolean constant.

--------------------


### 6. Call inside loop
Severity: low


Description

Calls inside a loop might lead to a denial-of-service attack.

If one of the destinations has a fallback function that reverts, `bad` will always revert.

Detailed description can be found [here](https://swcregistry.io/docs/SWC-103)


Recommendation

Favor pull over push strategy for external calls.

------------------------------

### 7. Missing events access control
Severity: low


Description

Detect missing events for critical access control parameters.

Detailed description can be found [here](https://swcregistry.io/docs/SWC-103)

    function depositAuction(uint256 _amount, bool _isUSDT) external payable {
            checkConditions(_amount);
            UserInfo storage user = userInfo[msg.sender];
            if (user.bnbAmount == 0 && user.usdtAmount == 0) { // add new user to the list
                addressList.push(msg.sender);
            }




Recommendation

Emit an event for user registration.


----------------

### 8. Block.timestamp
Severity: low

Description

Dangerous usage of block.timestamp. block.timestamp can be manipulated by miners.



Detailed description can be found [here](https://swcregistry.io/docs/SWC-103)


Recommendation

Avoid relying on block.timestamp.

--------------

### 9. immutable variable
Severity: optimazition


Description

STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE

Saving Storage Costs with Immutables.


Detailed description can be found [here](https://blog.soliditylang.org/2020/05/13/immutable-keyword/)

    address public offeringToken; // token address for sale
    uint256 public startTime;   // auction start time
    uint256 public endTime;     // auction end time


Recommendation

Some variable are never updated after deployed. so for these variable make immutable.

--------------------------

### 10. Extra gas use in for loop
Severity: low

Issue

        for (uint256 i = 0; i < addressList.length; i++) {
          \\
          \\
          }


Description

Detailed description can be found [here](https://hackmd.io/@totomanov/gas-optimization-loops)


Recommendation

    uint length=addressList.length;
    for (uint256 i; i < length; ++i) {
           \\
           \\
           }

------------------------------------------

### 11. Use modifier
Severity: optimization

issue
                  
    function depositeAuction(){
    checkConditions(_amount);
    }


Description



Detailed description can be found [here](https://swcregistry.io/docs/SWC-103)


Recommendation

use modifier to check/verify condition pre/post function exicution



#################################################################



In total, 7 issues were reported including:

2 medium severity issues.

4 low severity issues.

1 minor observation.

No critical security issues were found.


## Conclusion
