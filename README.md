# Ali_Khtami_Solidity13(learning from the video of Pattrick Collins)
### Withdraw fund

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

  function withdraw() public{

      for(uint256 funderIndex=0; funderIndex < funders.length; funderIndex++){

          address funder = funders[funderIndex]; //to access the first element or 0th element and gonna return an address for us to use
          // we gonna use the above line to reset our mapping 
          addressToAmountFunded[funder]=0;
      }

      // reset the array
      //here below the funders array is now equal to brand new address array with zero object to start with
      // if 1 in the first bracket then there will be one element to start with
      funders= new address[](0);
      // if we want to transfer fund to whomever is calling the withdrawal function we wil do the 
      //following
     // msg.sender.transfer(address(this).balance); //here 'this' refers to the whole 'akrkFundMe' contract
      // by using .balance we can get ethereum blockchain currency of this contract address 

      payable(msg.sender).transfer(address(this).balance);
      //here we typecast msg.sender form address typr to payable address type

      //send method

      bool sendSuccess= payable(msg.sender).send(address(this).balance);
      require(sendSuccess, "fail to send");

      //call

      (bool callSuccess, bytes memory dataReturned)= payable(msg.sender).call{value: address(this).balance}("")//here inside the bracket of call we gonna call function but now it is kept blank
     // here it gonna returned two variables 




  }
  
   
    

}



```

since we are gonna withdraw all funds out of this contract we also gonna reset in the above contract funders array in this line ```address[] public funders;```<br>
also reset  ```addressToAmountFunded```. Since we are withdrawing all funds those amounts should go down to zero<br>

for that we have to loop through the funders array which is this one ```address[] public funders;``` and update our mapping which is this line of code <br>
``` mapping(address => uint256) public addressToAmountFunded ;``` so that all funders have zero as we are withdrawing funs from them<br>

For this we gonna use something called ```for loop```<br>

A ```for loop``` is a way to loop through some type of index object or loop through a range of numbers or just do a task a certain amount of times repeating<br>

In ```for loop``` we will give the following <br>
```for(starting index,ending index,step amount)```<br>


In ``` function fund() public payable``` we update the amount ```addressToAmountFunded[msg.sender] = msg.value;``` whwnever we fund<br>
the contract<br>

when we withdraw the money from the contract at here ```function withdraw() public``` we gonna rsest it back to zero <br>
by this line ```addressToAmountFunded[funder]=0;```<br>

there are three differnt ways To send ether or native blockchain currency to whomever is calling the ```function withdraw() public``` function
1. transfer <br>
2. send<br>
3. call<br>

here ```transfer``` is the simplest and mostly used<br>

here we need to know that msg.sender is of address type and payable(msg.sender) is of payable address type<br>
In solidity in order to send native blockchain currency ethereum we need to work with ```payable address``` type<br>

https://solidity-by-example.org/sending-ether/ <br>

if we visit the link above we can find the issues of ```transfer``` method of sending ether which is <br>
transfer (2300 gas, throws error), if gas limits exceed 2300 gas it will return error<br>

but in case of send method it shows ```send (2300 gas, returns bool)``` which is it will not show error if <br>
exceeds ```2300``` gas but returns bool i.e whether it is successful or not <br>

```transfer``` method automatically reverts if the transaction fails <br>
```send``` method will revert only if we use this ```require(sendSuccess, "fail to send");``` statement <br>
```call``` is gonna be one of the first lower level commands that we use in solidity<br>
```call``` is actually incedibly powerful and we can use it virtually to call any function in all of the <br>
ethereum without having any ABI<br>


here in the above code in this line below <br>

```(bool callSuccess, bytes dataReturned)= payable(msg.sender).call{value: address(this).balance}("")``` <br>
the method call will call different function and if that function returns any value or data that will be <br>
stored at ```bytes dataReturned``` <br>

as here ``` (bool callSuccess, bytes memory dataReturned)``` , ```byte``` objects are arrays we use ```memeory```<br>
keyword before ```dataReturned```<br>


we simply modified the code below:

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


    address public owner;// this is a global variable



    //the constructor below will set up as the owner of the contract

     constructor(){

         owner= msg.sender; // owner is equal to msg.sender

     }


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

  function withdraw() public{

      for(uint256 funderIndex=0; funderIndex < funders.length; funderIndex++){

          address funder = funders[funderIndex]; //to access the first element or 0th element and gonna return an address for us to use
          // we gonna use the above line to reset our mapping 
          addressToAmountFunded[funder]=0;
      }

      // reset the array
      //here below the funders array is now equal to brand new address array with zero object to start with
      // if 1 in the first bracket then there will be one element to start with
      funders= new address[](0);
      // if we want to transfer fund to whomever is calling the withdrawal function we wil do the 
      //following
     // msg.sender.transfer(address(this).balance); //here 'this' refers to the whole 'akrkFundMe' contract
      // by using .balance we can get ethereum blockchain currency of this contract address 

      

      //call is the recommended as it forwards all gas and it has no limit and bool like sender

      (bool callSuccess, )= payable(msg.sender).call{value: address(this).balance}("");//here inside the bracket of call we gonna call function but now it is kept blank
     // here it gonna returned two variables but we need only one varaible
     require(callSuccess, "Call failed");







  }
  
   
    

}


```

Now in the above code if we use anybody can withdraw from this contract but we don't want everyone to withdraw from this contract<br>

We want only person who is collecting the fund able to withdraw the fund <br>
Withdraw function can only be called by the owner of the contract<br>
For that we have to make such settings so that whoever deploy this contract will be the owner of this contarct<br>

```function CallMeCaptain()``` this function will take two transactions and it will be much annoying and for that <br>
there is a solution in solidity which is known as ```constructor```, ```constructor``` is gonna be a function that gets called<br>
immedietly if we deploy this contract <br>

if we set inside the ```constructor()``` function , ```minimumUSD=2``` it will no longer be ```50 * 1e18``` <br>

here  ```msg.sender``` inside ```constructor()```  gonna be whoever deploying the contract<br>
     

Now we will modify the ```withdraw``` function below so that only the owner of this contract wil be able to call this function<br>


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


    address public owner;// this is a global variable



    //the constructor below will set up as the owner of the contract

     constructor(){

         owner= msg.sender; // owner is equal to msg.sender

     }


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

  //we use modifier in the function below

  function withdraw() public onlyOwner{

      

      for(uint256 funderIndex=0; funderIndex < funders.length; funderIndex++){

          address funder = funders[funderIndex]; //to access the first element or 0th element and gonna return an address for us to use
          // we gonna use the above line to reset our mapping 
          addressToAmountFunded[funder]=0;
      }

      // reset the array
      //here below the funders array is now equal to brand new address array with zero object to start with
      // if 1 in the first bracket then there will be one element to start with
      funders= new address[](0);
      // if we want to transfer fund to whomever is calling the withdrawal function we wil do the 
      //following
     // msg.sender.transfer(address(this).balance); //here 'this' refers to the whole 'akrkFundMe' contract
      // by using .balance we can get ethereum blockchain currency of this contract address 

      

      //call is the recommended as it forwards all gas and it has no limit and bool like sender

      (bool callSuccess, )= payable(msg.sender).call{value: address(this).balance}("");//here inside the bracket of call we gonna call function but now it is kept blank
     // here it gonna returned two variables but we need only one varaible
     require(callSuccess, "Call failed");


     
}


modifier onlyOwner {
         require(msg.sender == owner, "Sender is not owner!");// to check is msg.sender is equal to owner
         _;
     }

  
   
    

}




```

     


If we want to use this line ```require(msg.sender == owner, "Sender is not owner!");``` in every functions of the contract<br>
we have to use something called modifier<br>

Modifier is gonna be a keyword that we gonna use in the function declaration to modify the function with that functionality<br>
by adding ```modifier``` to thsi function ```function withdraw() public onlyOwner``` we are giving command to the function <br>
at fisrt do whatever in the ``` modifier onlyOwner``` and do the rest <br>, ``` _;``` means execute rest of the code <br>

Now we will implement on test net below and it will be sucessfully deployed<br>

![w00](https://github.com/C191068/Ali_Khtami_Solidity13/assets/89090776/f662f428-369a-4f2d-962d-dcd7fc8d6f14)
Figure1: When we click the ```Deploy``` button our metamask window pops up show the amount required fopr the transaction<br>




![w1](https://github.com/C191068/Ali_Khtami_Solidity13/assets/89090776/915dfdb4-d67b-4f7b-b5ec-de72ddc154ff)
Figure2: successfully deployed and transaction occured<br>


![w2](https://github.com/C191068/Ali_Khtami_Solidity13/assets/89090776/ab69ed5f-9b6e-49dc-934b-a31ce1522831)
Figure3: in the ddeployed contract we can see that when we click ```mininmumUSD``` button it shows ```$50```<br>
when we click ```owner``` button it shows the account addess of our metamask wallet account <br>

To set the ```Value``` option at ```Deploy and Run transaction``` tab at my repository https://github.com/C191068/Ali_Khatami_solidity_09
I have shown a calculation which i again want to repeat below :

As the current price of ETH in terms of USD is $1,816.75<br>

here our minimum value is set to $50

therefore 50/1816.75 = 0.027+0.006 = 0.033 which is approximately enough and which is about 33000000000000000 WEI<br>

One think to keep in mind by clicking ```fund``` we gonna actually pay money to this contract <br>

by clicking ```withdraw``` button we gonna take money from this contract <br>


![w3](https://github.com/C191068/Ali_Khtami_Solidity13/assets/89090776/d065111a-cf9a-4609-81cd-5ebbcde7d860)
Figure3: When we paste the above calculated value of WEI at the value option and click ```fund``` button <br>
the metamask windows pop up and shows the amount of Sepolia ETH we gonna pay to this contract which is ```0.033 Sepolia Eth```<br>


![w4](https://github.com/C191068/Ali_Khtami_Solidity13/assets/89090776/84c3497a-875d-4f64-b60a-d9066a16dc3b)
Figure4; after clicking the ```Confirm``` button successful transaction occured<br>

![w5](https://github.com/C191068/Ali_Khtami_Solidity13/assets/89090776/6d31e487-63e4-48d0-ae21-b57b0e255582)
Figure5: on etherscan on search button when we paste the address of the deployed contract we see that <br>
two types of transactions occured one due to contract deployment and another due to clicking of the ```fund``` button <br>

![w6](https://github.com/C191068/Ali_Khtami_Solidity13/assets/89090776/48e4e269-87ab-498c-b8a4-9737c611dea1)
figure6: if we add again ```0.033 Eth``` the amount will rise up to ```0.066 Eth``` this is what we expected<br>

![w7](https://github.com/C191068/Ali_Khtami_Solidity13/assets/89090776/287ddf00-dcee-44dc-8cc9-54f9685cf164)
Figure7: if we copy our metamask account address to ```addresstoAmountFunded``` button field and click the button<br>
we see that the amount which is funded shows up <br>


![w8](https://github.com/C191068/Ali_Khtami_Solidity13/assets/89090776/034cb104-40aa-4412-89a8-f7d975f7a7e7)
Figure8: at ```funders``` button field when we write 0 and 1 index we see our metamask wallet account address<br>

![w9](https://github.com/C191068/Ali_Khtami_Solidity13/assets/89090776/2d79e7e8-df29-4b31-a0bc-0b64f84f355b)
Figure9; but see error when we type 2<br>



























