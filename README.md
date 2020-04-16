# csc4980_homework4
This is a walkthrough of the tutorial found here: https://coursetro.com/posts/code/102/Solidity-Mappings-&amp;-Structs-Tutorial

For this project, you will need to download Truffle and Solidity on your machine. Assuming you are using a Linux-based system, the following commands will download each, using the npm package manager:

```
npm i -g truffle
npm i -g solc
```

Now that you have downloaded the necessary software, move to the directory that you would like to store your project in and create a subdirectory for it. I named my subdirectory simply "homework4," but you can name it whatever you like.

```
mkdir homework4
cd homework4
```

Now that you have moved to your newly made directory, you need to initialize truffle within the directory using the following command:

```
truffle init
```

At this point, we need to create a solidity file for our contract. We could do this manually by writing a .sol file and copying it to our contracts directory, or we could use the following code to more quickly create a file there:

```
truffle create contract Courses
```

I call the contract Courses here, as that is the name of the contract in the tutorial. Now we should go to the contracts directory and edit the Courses.sol file, which will be there along with a Migrations.sol file. The following code should be pasted into your Courses.sol file:

```
pragma solidity ^0.5.16;

contract Courses {

  struct Instructor {

    uint age;
    string fName;
    string lName;
  }

  mapping (address => Instructor) instructors;
  address[] public instructorAccts;
    constructor() public{
    }


  function setInstructor(address _address, uint _age, string memory _fName, string memory _lName) public {
    //var instructor = instructors[_address];

    instructors[_address].age = _age;
    instructors[_address].fName = _fName;
    instructors[_address].lName = _lName;

    instructorAccts.push(_address) -1;
  }

  function getInstructors() view public returns(address[] memory) {
    return instructorAccts;
  }

  function getInstructor(address _address) view public returns (uint, string memory, string memory) {
    return (instructors[_address].age, instructors[_address].fName, instructors[_address].lName);
  }

  function countInstructors() view public returns (uint) {
    return instructorAccts.length;
  }
}
```

The "pragma solidity" statement at the beginning of the file specifies which compiler should be used for the program, so your compiler version may be different than mine. If compilation fails, check your solidity compiler version with the following command:

```
truffle version
```

In order to deploy our contract, we now need to create another file, called "2_deploy_contracts.js" in the migrations directory, and paste the following code into it:

```
var Courses = artifacts.require("./Courses.sol");
module.exports = function(deployer) {
  deployer.deploy(Courses);
};
```

Now we compile. Change back to the main project directory ("homework 4" for me) and use:

```
truffle compile
```

This should compile your project with no errors. Now, to enter a truffle development console, use:

```
truffle develop
```

This will initiate a truffle console, as well as list long Accounts and Private Keys strings; one or more Accounts strings will be useful later. At this point, it may be necessary to clear any previous migrations you may have before testing will work. Do this by using:

```
migrate --reset --all
```

Now for testing our contract functions. We will input function calls directly into the truffle develop console, passing variable values as necessary. First, let's create an Instructor object. The setInstructor function looks like so:

```
function setInstructor(address _address, uint _age, string memory _fName, string memory _lName) public {
    instructors[_address].age = _age;
    instructors[_address].fName = _fName;
    instructors[_address].lName = _lName;    

    instructorAccts.push(_address) -1;
  }
```
So when calling it, we must pass in a variable for the instructor's address (for which we will choose one of the Accounts strings provided to us upon entering the truffle develop console), age, first name, and last name. Use this command to do so, using your desired names, age, and address variables:

```
Courses.deployed().then(function(instance){return instance.setInstructor("0x279cbd3be9f78e37276515fde7573ad33385f68d", 26, "Aaron", "Morrison");})
```

Something like this should be returned in the console:

```
{ tx: '0x6ca226b930396300e61fd171db7f4e3869cc5336bf046abe6c832bd3c751fdea',
  receipt: 
   { transactionHash: '0x6ca226b930396300e61fd171db7f4e3869cc5336bf046abe6c832bd3c751fdea',
     transactionIndex: 0,
     blockHash: '0x0fa7e23f70b776f6227802405945306750f40eaf9cf3af0ab856a922d059f567',
     blockNumber: 5,
     from: '0x279cbd3be9f78e37276515fde7573ad33385f68d',
     to: '0x1d99ed26102a5e75a5bb9cf61ea285427b20c295',
     gasUsed: 127909,
     cumulativeGasUsed: 127909,
     contractAddress: null,
     logs: [],
     status: true,
     logsBloom: '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
     rawLogs: [] },
  logs: [] }

```

To test the getInstructors function, no variable is necessary, just input the following:

```
Courses.deployed().then(function(instance){return instance.getInstructors();})
```

The address values of all instructor objects should be returned like so:

```
[ '0x279CbD3BE9F78e37276515FdE7573AD33385f68d' ]
```

To test the getInstructor function, you can pass it any address variable, but no instructor object will be recognized if a valid variable isn't used:

```
Courses.deployed().then(function(instance){return instance.getInstructor("0x279cbd3be9f78e37276515fde7573ad33385f68d");})
```

Something like this should be returned in the console if you input a valid address variable:

```
Result { '0': <BN: 1a>, '1': 'Aaron', '2': 'Morrison' }
```

To test the countInstructors function, no variable is necessary, just input the following:

```
Courses.deployed().then(function(instance){return instance.countInstructors();})
```

The number of Instructors should be returned like so:

```
<BN: 1>
```
