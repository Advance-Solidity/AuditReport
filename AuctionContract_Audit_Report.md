# Auction Smart Contract audit report.

## Summary

An auction smart contract is a self-executing agreement between the Owner and fundraisers,on which the process is automated and put into the blockchain ecosystem to be made transparent.
The minimal amount that an initial coin offering (ICO) must raise is referred to as a "softCap." Users can deposite their money during auction. The ICO may be discontinued and the monies gathered refunded to participants if it is unable to raise that amount.

## In Scope
1. [testAuction.sol](https://gist.github.com/yuriy77k/edf8b3bcddbc3d43967f5765edf4727e#file-testauction-sol)

      Excluded:
         `IERC20 transfer` function has not checked due to interface contract of ERC20.

## Finding

   Here are some of commonly known and more vulnerability found.
   
| **Vulnerability** | **severity** | 
| --------------- | --------------- |
|Floating Pragma |low| 
| Divide before multiply | Medium |
|Reentrancy | high |
| Unchecked transfer | High  | 
| Boolean equality | Informational |
|  Call inside loop|low |
|Missing events access control | low| 
| TimeStamp dependency | low |

   


   

## Security issues

### 1. Floating Pragma
   Severity: low


   Description:
   
By locking the pragma, contracts are prevented from unintentionally being deployed with the latest compiler, which may involve a high risk of unknown bugs. Contracts may also be deployed by others and the pragma indicates the compiler version intended by the original authors.

   Detailed description can be found [here](https://swcregistry.io/docs/SWC-103)


   Recommendation

---------------------

### 2. Divide before multiply
Severity: Medium


Description:

 In Solidity integer division might truncate data. Unless the limit of a smaller type makes this problematic, rearranging math such that multiplication comes before division is typically a smart idea.
 
 
[issue](https://gist.github.com/yuriy77k/edf8b3bcddbc3d43967f5765edf4727e#file-testauction-sol-L87)

      In function claimTokens(){
        //
        //
      uint256 amountOfTokens = supplyToDistribute * userDepositedUSD / totalRaisedUSD;
      //
      //
      }

Detailed description can be found [here](https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply)

Recommendation

Consider ordering multiplication before division

------------------------------

### 3. Reentrancy vulnerabilities
Severity: high


Description

Detailed description can be found [here](https://github.com/crytic/slither/wiki/Detector-Documentation#reentrancy-vulnerabilities)

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



Detailed description can be found [here](https://github.com/crytic/slither/wiki/Detector-Documentation#block-timestamp)


Recommendation

Avoid relying on block.timestamp.

--------------

### 9. immutablity
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




Recommendation

use modifier to check/verify condition pre/post function exicution





## Conclusion

There were several faults that were of High and Medium severity. Additionally, several recommendations and best practices are given in order to enhance the security posture and code quality.
