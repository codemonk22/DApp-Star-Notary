# DApp-Star-Notary
#### Decentralized Star Notary Service Project with ERC721. 
The StarNotary smart contract, which has been deployed on the Rinkeby public test network, provides for the ownership, proof, and transfer/sale of stars. This project is based on the Ethereum blockchain's ERC-721 NFT, or unique tokens. You can share, trade, or transfer ownership of this tokenn to any of your pals.

# Tools used in this project

````Truffle Version: 5.0.2
Openzeppelin Library 2.3
Truffle hdwallet Provider 1.0.17
Webpack Dev Server
Web3
NodeJS 12.22.12
````
```
# Check Node version
node -v
# Check NPM version
npm -v
```
## List of Functions used in the Smart contract:
Default functions are available in the boilerplate code
```createStar()
putStarUpForSale()
make_payable()
buyStar()
```
#### Newly created Function
```
lookUptokenIdToStarInfo()
exchangeStars()
transferStar()
```
#### Name and Symbol of the Star
```
constructor() ERC721('TN Grizzlies Star', 'TNGS') {}
```
![image](https://user-images.githubusercontent.com/105297413/168395581-64bddfd6-8516-491d-8ce9-eeb53e6d3ca6.png)

### lookUptokenIdToStarInfo()
Allows looking up the star name by its id. By indexing a mapping variable called tokenIdToStarInfo the name of the star is returned as a string.

```// Implement Task 1 lookUptokenIdToStarInfo
    function lookUptokenIdToStarInfo (uint _tokenId) public view returns (string memory) {
        //1. You should return the Star saved in tokenIdToStarInfo mapping
        Star memory star = tokenIdToStarInfo[_tokenId];
        string memory starName = star.name;
        return starName;
    }
```
### exchangeStars()
Allows for the exchange of two stars. Two star IDs are inputs, and the address owner can be determined using "ownerOf." We can tell the sender is the owner of one of the stars by comparing the sender address (the one making the request) to the ownerOf ID address. This forbids the trading of any two stars. Stars can be transferred using the transferFrom function. The ERC721 contains this function.
```
// Implement Task 1 Exchange Stars function
function exchangeStars(uint256 _tokenId1, uint256 _tokenId2) public {
    //1. Passing to star tokenId you will need to check if the owner of _tokenId1 or _tokenId2 is the sender
    address sender = msg.sender;
    //2. You don't have to check for the price of the token (star)
    //3. Get the owner of the two tokens (ownerOf(_tokenId1), ownerOf(_tokenId1)
    address address1 = ownerOf(_tokenId1);
    address address2 = ownerOf(_tokenId2);
    //4. Use _transferFrom function to exchange the tokens.
    if (sender == address1){
        //token 1 is owned by sender
        _transferFrom(address1, address2, _tokenId1);
        _transferFrom(address2, address1, _tokenId2);
    }else if (sender == address2){
        // token 2 is owned by sender
        _transferFrom(address2, address1, _tokenId2);
        _transferFrom(address1, address2, _tokenId1);
    }   
}
```
#### transferStar()
```
Allows you to move a star from one location to another. Inputs include the sender's address and the receiver's star ID. If the sender address owns the star, it is transmitted via the ERC721 function.

// Implement Task 1 Transfer Stars
function transferStar(address _to1, uint256 _tokenId) public {
    //1. Check if the sender is the ownerOf(_tokenId)
    address address1 = ownerOf(_tokenId);
    //2. Use the transferFrom(from, to, tokenId); function to transfer the Star
    if(msg.sender == address1){
        _transferFrom(address1, _to1, _tokenId);
    }
}
```
### Testing Contract Function
#### TestStarNotary
Each test statement deploys an instance of the contract. Depending on the sort of test, stars are also awarded. The address given by truffle, are owned the stars.

#### Deployed token name and symbol

Calling out public variables within the contract and checking with conditional statements "assert.equal()" are used to test token name and symbol. The name of a star can also be tested by establishing a star with the name "Mystar" and then testing it.
```
it('can add the star name and star symbol properly', async() => {
    //1. Create a star
    let instance = await StarNotary.deployed();
    let user1 = accounts[1];
    let starId = 6;
    await instance.createStar('Mystar', starId, {from: user1});

    //2. Call the name and symbol properties in your Smart Contract and compare with the name and symbol provided
    let ContractName = await instance.name(); //get Token name
    let ContractSymbol = await instance.symbol(); // get token symbol
    let starLookUp = await instance.lookUptokenIdToStarInfo(starId); //get star name

    assert.equal(ContractName,'StarToken');
    assert.equal(ContractSymbol,'SRT');
    assert.equal(starLookUp,'Mystar');
});
```
#### Testting the exchange of stars
To test exchange of stars, you'll need two addresses, both of which have exchange stars. The starIds are then passed to the contract's exchangeStars method. The ownership of the stars will be in the other address to see if the exchange worked.
This table my help with accounts to stars ownership.
```
it('lets 2 users exchange stars', async() => {
    // 1. create 2 Stars with different tokenId
    let instance = await StarNotary.deployed();
    let user1 = accounts[0];
    let user2 = accounts[1];
    let tokenId1 = 7;
    let tokenId2 = 8;
    await instance.createStar('Awesome Star for Token 7!!', tokenId1, {from: user1});
    await instance.createStar('Awesome Star for Token 8!!', tokenId2, {from: user2});
    // 2. Call the exchangeStars functions implemented in the Smart Contract
    await instance.exchangeStars(tokenId1, tokenId2, {from: user1});
    // 3. Verify that the owners changed
    assert.equal(await instance.ownerOf(tokenId1), user2);
    assert.equal(await instance.ownerOf(tokenId2), user1);

});
```
#### Test transfer stars
Testing transfer stars necessitates two addresses, each with a single star. After entering the receiver's address and the sender's star ID into transferStar. The test will then pass by proving that the receiver's address is now the new owner of the star.
```
it('lets a user transfer a star', async() => {
    // 1. create a Star with different tokenId
    let instance = await StarNotary.deployed();
    let user1 = accounts[0];
    let user2 = accounts[1];
    let tokenId = 9;
    await instance.createStar('Awesome Star for Token 9', tokenId, {from:user1});
    // 2. use the transferStar function implemented in the Smart Contract
    await instance.transferStar(user2, tokenId, {from:user1});
    // 3. Verify the star owner changed.
    assert.equal(await instance.ownerOf(tokenId), user2);
});
```
#### Front End DAPP
```
functions:

createStar: allows a user to create a star on the blockchain with a metamask address

lookUp: allows a user to lookup a star name by ID through the use of a contract function.
```
![image](https://user-images.githubusercontent.com/105297413/168397398-24424395-f22a-42a4-9cca-161158138685.png)
## Project Environment Setup
```
# Unsinstall any previous version
npm uninstall -g truffle
# Install
npm install -g truffle
# Specify a particular version
npm install -g truffle@5.0.2
# Verify the version
truffle version
```
```
Metamask: 5.3.1 - If you need to update Metamask just delete your Metamask extension and install it again.

Ganache - Make sure that your Ganache and Truffle configuration file have the same port.
```
````
Other mandatory packages:
cd app
# install packages
npm install --save  openzeppelin-solidity@2.3
npm install --save  truffle-hdwallet-provider@1.0.17
npm install webpack-dev-server -g
npm install web3
````
```
Run the application
Clean the frontend

cd app
# Remove the node_modules  
# remove packages
rm -rf node_modules
# clean cache
npm cache clean
rm package-lock.json
# initialize npm (you can accept defaults)
npm init
# install all modules listed as dependencies in package.json
npm install
```
```
Start Truffle by running

# For starting the development console
truffle develop
# truffle console

# For compiling the contract, inside the development console, run:
compile

# For migrating the contract to the locally running Ethereum network, inside the development console
migrate --reset

# For running unit tests the contract, inside the development console, run:
test
```
![image](https://user-images.githubusercontent.com/105297413/168393356-c40287cb-bbb7-4b68-8e38-4599d9d348ea.png)

![image](https://user-images.githubusercontent.com/105297413/168393585-1b30b1e9-3a11-45d3-a0ed-89fba6b32012.png)


```
Frontend - Once you are ready to start your frontend, run the following from the app folder:

cd app
npm run dev
```
![image](https://user-images.githubusercontent.com/105297413/168393808-bc60d601-b1c3-486a-9e23-5b3994c719a3.png)


![image](https://user-images.githubusercontent.com/105297413/168398165-b412e8da-1828-47ec-9ae7-a594cb5cfa35.png)

```
Added a new Rinkeyby Test Network in my local Metamask client:

Network Name	New RPC URL	Chain ID
Private Network 1	http://127.0.0.1:9545/	1337
The chain ID above can be fetched by:
```
![image](https://user-images.githubusercontent.com/105297413/168394359-ac1ac7df-973b-427b-84b2-998ea98a92d3.png)

```
cd app
node index.js
```

## Star Deployed in Rinkbey Network
![image](https://user-images.githubusercontent.com/105297413/168394710-e38746be-37a1-4298-847b-11fec0c30128.png)


Please view the newly created star Token on [Etherscan](https://rinkeby.etherscan.io/tx/0x07d7dd5c1b6885aedb7488e45c36537e5a409bf33dd4d0ee2746d8d3b599c3bc).


## Etherscan Contract Display
![image](https://user-images.githubusercontent.com/105297413/168394741-29deae4a-f342-4dc1-a7a3-8f034160a86b.png)

### Troubleshoot:

 Error 1
 ```
'webpack-dev-server' is not recognized as an internal or external command
Solution:

Delete the node_modules folder, the one within the /app folder
Execute npm install command from the /app folder
After a long install, everything will work just fine!
```
 Error 2
```ParserError: Source file requires different compiler version. 
Error: Truffle is currently using solc 0.5.16, but one or more of your contracts specify "pragma solidity >=0.X.X <0.X.X".
Solution: In such a case, ensure the following in truffle-config.js:

// Configure your compilers  
compilers: {    
  solc: {      
    version: "0.5.16", // <- Use this        
    // docker: true,
    // ...
Raise a PR or report an Issue
Feel free to raise a Pull Request if you find a bug/scope of improvement in the current repository.
```
 If you have suggestions or facing issues, you can log in issue.
