# NO BULLSHIT: GUIDE TO MEV

My MEV journey started with Solidity. A must learn if you're going to do MEV and it will be a prerequisite for learning assembly and understanding a lot of alpha that will be presented here during our adventure.

 Part 0: Learn nodeJS. I assume you have this done, else very easy to learn. I'll let you do this

 ### Part 1: Learn The Solidity Language (this is all you'll need woo!): (duration 3h34 minutes)
 https://www.youtube.com/watch?v=M576WGiDBdQ
 - PART 1: 1:31:00 to 3:26:00
 - PART 2: 11:49:05 to 13:29:15
 - Conclusion (5 minutes): 16h:14:16
 
 
 ### Part 2: Learn ETHERS 
 
Once I learned Solidity, the next step was to learn ethersJS. This is necessary to connect your backend code to any blockchain and interact with it. There also exists web3JS but we will learn ethersJS because it is faster, better documented in both nodeJS and the Rust language
 
 https://www.youtube.com/watch?v=yk7nVp5HTCk&ab_channel=DappUniversity
 
 With that we are ready to do start MEV-ing !
 
 ### Part 3: Understand Arbitrage, Liquidations, flashloans
 
 After a lot of research and wasting a lot of time trying to understand liquidations, arbitrage and flashloans as a beginner I came across EatTheBlocks Profitable Flashloans Course. You probably know him if you have tried learning MEV alone, his course is truly the best ressource you'll find and you will understand Liquidations, Flashloans, Arbitrage all while using Solidity and ETHERS which is what we just learned before! (this is not sponspored)
 
https://pro.eattheblocks.com/courses/enrolled/958603

The course if very cheap for what you learn but if you can't afford it, you can hop directly to Flashbot's simple arbitrage BOT explanatory video and watch it at least 2-3 times. 

https://www.youtube.com/watch?v=wn8r674U1B4&t=3387s&ab_channel=RobertMiller 

Close to the ending of the Flashbot's simple arbitrage they go over payloads, calldata and stuff which you'll inevitably find hard to understand. But don't worry. Here I am, the work and research is already done for you

### PART 4: CALL FUNCTIONS WHITOUT NEED OF CONTRACT ABI (interfaces etc...) SAVE (A LOT OF) GAS LIKE THIS

READ ALL THIS. TON OF ALPHA. YOU'RE WELCOME. 

- [DATA PAYLOADS](https://medium.com/swlh/understanding-data-payloads-in-ethereum-transactions-354dbe995371)
- [CALL, DELEGATE CALL...](https://kushgoyal.com/ethereum-solidity-how-use-call-delegatecall/?fbclid=IwAR3IUpFhGjOC_Lwo2Wu5cNg5EChT5m3fqj6hAdnx5Iz31O9DkZ_hX7_gg8M#:~:text=Solidity%20has%20the%20call%20function,to%20transfer%20ether%20to%20addresse)
- [Abi.encode and abi.decode](https://medium.com/coinmonks/abi-encode-and-decode-using-solidity-2d372a03e110)
 
Basically what they do in simple Arbitrage Bot is to "populate" a transaction using ethers to obtain the payloads which they can then call in their back-end code. If you use payloads in you're backend code (JS) it will be to call a contract with the encoded parameters. Used in solidity it will be to call other contracts from your contract.


### PART 5: GAS OPTIMIZATIONS ALPHA (A HELLA LOT)

By now, you might have found a strategy that you want to try. Here are almost all the gas optimizations you can use condensed all in here

- [A LOT OF GAS OPTIMIZATIONS BY 0xMasashi](https://twitter.com/0xMasashi/status/1468994152390176770)
- [MORE GAS OPTIMIZATIONS](https://m1guelpf.blog/d0gBiaUn48Odg8G2rhs3xLIjaL8MfrWReFkjg8TmDoM?fbclid=IwAR1MztUOuQvKYX7NyEYc6LwwGK66KYXMivS-_gLIMpGb4ym9chiuBTfg4Kk)
- [Advanced gas optimzation, ftcn name, Packing your calldata, CREATE2 and more](https://medium.com/joyso/solidity-save-gas-in-smart-contract-3d9f20626ea4)
- [Huge Compilation of gas Optimizations](https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/3)

1. fixed size is better than dynamic size (array[5] instead of array[])
2. set optimizer to high value. (1 round = less cost to deploy, costly fctns call. 100 000 rounds = expensive deploy, cheap calls). The less opcodes you have, the better

EXTRA
1. leave 1 wei of token in contract to avoid initializing storage twice for a same token (Basically when transfering token from you're contract send the amount minus 1 wei)
2. When calling swap() V2 AMM's function (Pancake, Sushi, Uni, apeswap etc) for a multihop arbitrage, set the ‘to’ field to the address of the next Uniswap pair in your arbitrage hop. This avoids having to transfer the received tokens from your smart contract address to the next pair on every hop
3. Pack your calldata. Normally, each argument in your calldata will be padded with leading 0s by the abiEncode function. This means you’ll often end up with extra 0s in your calldata that do nothing other than pad. Extra bytes mean extra gas. If you get your hands a bit dirty using inline assembly, you can bypass solidity’s standard abi decoder for calldata and just slice the calldata bytearray yourself:
https://gist.github.com/0xmebius/f161abff38b88c9005db31d47e39bbe0

4. Another leading 0 hack. When you define a function foo(uint x), the function signature (the first 4 bytes of your transaction calldata used to select which function you are calling) will be the first 4 bytes of Keccak-256 hash of foo(uint x). You can brute force various function names so that Keccak256(FUNCTIONNAME(uint x))[:4] == 0x0000. This saves some gas since 0 byte calldata is slightly cheaper than non zero byte calldata
https://github.com/fxfactorial/cheap-name-. However this will make it easier for people to see you in the mempool. Call it a psyop ?

5. You can pretend to make a function call that is not what you're actually calling. For example you can pretend to approve a token by force bruting your
function signature to have the same one as the approve function selector. This will appear on etherscan as if you're approving something when in reality you're not. Think about it. 

GIT : 

This code uses Rust which we have not learned yet, but the code can be easily used straight away whitout needing to understand rust. Hit me up on telegram if you need help with this.

### PART 6: ASSEMBLY & THE EVM:

If you want to go futher in the gas optimizations rabbit hole, you can learn assembly by first understanding how tf ethereum works (EVM and Stuff) and then learning actual assembly. I personally do not recommend trying to overoptimize your contract gas usage as it is time extensive and you should focus on finding new strategies instead. But you'll learn a ton by doing this and it is all very intersting.

- [How tf Ethereum works](https://www.preethikasireddy.com/post/how-does-ethereum-work-anyway) don't want to read the yellowpaper? Read this
- *[A Playdate with EVM](https://femboy.capital/evm-pt1) Amazing intro of sorts to EVM
- *[EVM DEEP DIVE 3 parts](https://substack.com/profile/80455042-noxx?fbclid=IwAR3DfpZfgit_AVbyLIIr7LoFsHsMDvN466F-ArC06Rid_S41dGQw1wTWW68)
- *[VERY GOOD MEDIUM](https://jeancvllr.medium.com/solidity-tutorial-all-about-assembly-5acdfefde05c)
- [WATCH THIS AND TRY TO GO ALONG UNTIL YOU MASTER](https://www.youtube.com/watch?v=RxL_1AfV7N4&t=2842s)


### ADVANCED ARBITRAGE PART 7: Convex Optimization Ressources and DFS arbitrage (REPO) PART 8 might interest you first though

By now you should have learned a ton. If you're ready to go next level, This is what we pros use. Convex Optimization and Optimal Path searching.

- [An Analysis of Uniswap Markets](https://arxiv.org/pdf/1911.03380.pdf) Section 2.1 explains the optimization problem for optimal arbitrage routing
- [Complex DFS ARBITRAGE Code Implementation](https://github.com/ccyanxyz/uniswap-arbitrage-analysis?fbclid=IwAR1qsAiM_DAOBWGtp5yAhRF7hHHCFSMzFzA4YIBfgNQvdJuR79J8QlZiPbk)
- [Convex Optimization](https://baincapitalcrypto.com/introducing-cfmmrouter-jl/)
- [CONVEX OPTIMIZATION RESSOURCES](https://twitter.com/0x_emperor/status/1516753295024726019)

### PART 8: LEARN TO BE A PIRATE

Every catastrophe is an opportunity. Millions of money are stolen each month from contract vulnerabilites and security exploits. Almost every one sucks at solidity and have no idea how Smart Contract Security works and how easily their contract can exploited. By understanding smart contract vulnerabilities you will be able to upgrade your MEV-ing.

However, I found that no one teached this freely. But again, EatTheBlocks has an amazing course on this and you'll learn a lot 

 PART 1 (smart contract vulnerabilities) : https://pro.eattheblocks.com/p/smart-contract-security-101
 - Here You will learn about Access Control, Tx.Origin, Accessing Unencrypted Data, Overflow, Reentracy Attacks, Denial of Service, Proxy and Upgradeability, Weak Randomness, Replay Attacks How to exploit and how to solve
 
 PART 2 (learn to find those vulnerabilities via dynamical methods and analysis): https://pro.eattheblocks.com/courses/enrolled/1462159 
 
 If again you can't afford this, consensys has a compilation of all known Attacks which you can learn about
 
 https://consensys.github.io/smart-contract-best-practices/attacks/
 
 
 After for pratice, you can complete these games where you basically have to exploit a contract correctly that contains an exploit.
 
 
https://ethernaut.openzeppelin.com/level/0x5732B2F88cbd19B6f01E3a96e9f0D90B917281E5



### Useful information.

 
READ BERTCMILLER COMPILATION OF all his MEV related threads in chronologic order. Contains a lot of useful info.

https://twitter.com/bertcmiller/status/1402665992422047747



PART 2 COMING SOON...

