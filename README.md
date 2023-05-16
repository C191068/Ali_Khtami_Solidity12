# Ali_Khtami_Solidity12(learning from the video of Pattrick Collins)
### Basic Solidity For Loop

As all the funders have funded our ```akrkFundMe``` contract , we will now withdraw fund from ths contract , so that funds could be used to buy things<br>
for that we made change in the code below of ```akrkFundeMe.sol``` <br>\

```

//SPDX-License-Identifier:MIT

pragma solidity ^0.8.8;

import "./akrkPriceConvertor.sol";

contract akrkFundMe  {

    using akrkPriceConverter for uint256;

    uint256 public minimumUSD=50 * 1e18; //as getConversionRate() returns 18 zeroes after decimel place
 
    address[] public funders;// we create an address array make it public 
// we have use payable keyword with the function below to make it payable with any native blockchain currency
    //below we have done mapping of addresses to how much money each of the people sent
    mapping(address => uint256) public addressToAmountFunded ;

    function fund() public payable {
     
     //as we want to convert msg.value to USD we made the following changes
       
        require(msg.value.getConversionRate() >= minimumUSD , "Not send enough");// to get accees to the 'value' at deploy at run transaction tab
        //1e18 is 1 ether, here the value must be greater than 1 ether
        //require is a checker it checks whether the value is greater than 1 ether
        // if not it is going to revert with an error messsage shown above
        //adding funders to the array 
         funders.push(msg.sender);
         addressToAmountFunded[msg.sender] = msg.value;// It is for when somebody funds our contract

    }// To send money. We made it public so that anybody can call it

  //The function below is created so that we can get price of ethereum in terms of USD , so that we can convert msg.value to USD
//Both functions are public because we can do whatever we want with them
//By using the getPrice() function below we are going to interact with contract outside of our project
  

  //below we will create a withdraw function

  function withdraw() public{}
  
   
    

}

```

since we are gonna withdraw all funds out of this contract we also gonna reset in the above contract funders array in this line ```address[] public funders;```<br>
also reset  ```addressToAmountFunded```. Since we are withdrawing all funds those amounts should go down to zero<br>

for that we have to loop through the funders array which is this one ```address[] public funders;``` and update our mapping which is this line of code <br>
``` mapping(address => uint256) public addressToAmountFunded ;``` so that all funders have zero as we are withdrawing funs from them<br>

For this we gonna use something called ```for loop```<br>

A ```for loop``` is a way to loop through some type of index object or loop through a range of numbers or just do a task a certain amount of times repeating<br>






