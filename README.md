# nft-stacks-clarity-starter
A tutorial for you to learn how to deploy your NFT collection on the Stacks chain using clarity lang

Welcome 👋 I am Sneha, I'll be your instructor for the day :) 

Today we will be creating our own NFT collection on the Stacks chain using Clarity. Now before we get started you must be curious as to why are we using Stacks and not other chains for the same? Well here's why 

 - Stacks is Layer 1 Blockchain solution powered by the security of Bitcoin 🪙
 - NFTs on Stacks are extremely easy to build on Stacks using their SIP-009 Standard 🔥
 - Clarity the smart contract language behind Stacks is optimised for predictability and security 🦺 Which inevitably means we don't need to worry about our funds getting locked in the contract/ hackers 🏴‍☠️ trying to steal our money 💵

So now that we know why let's get started right away 🙌 

## Local Environment Setup

### Clarinet
Clarinet is a Clarity runtime packaged as a command line tool, designed to make it easy to write, test, and deploy Clarity smart contracts. Clarinet uses the Clarity REPL under the hood. It is self-contained and provides a built-in testing environment to write tests for your smart contracts. 

#### How to Install Clarinet?
Download one of the prebuilt binaries from the [GitHub releases page](https://github.com/hirosystems/clarinet/releases/) particular to your platform, extract it, and then move it to a folder where your command line interface can find it:

macOS & Linux: copy the clarinet binary to `/usr/local/bin`.  
Windows: copy the clarinet.exe binary to `C:\Windows\System32`. (A better solution is in the works.)  

You can verify Clarinet is installed properly by running `clarinet --version` in your favourite Terminal emulator.

## Coding Time ⏲️

### Create a new Project using Clarinet  

We will create a new project called nft-stacks using Clarinet. Type the following in the Terminal screen:


`clarinet new nft-stacks`

### Create a new Contract using Clarinet  

First cd into this directory by running the following command - 

`cd nft-stacks`

Then, We will create a new contract inside our project folder called nft-stacks using Clarinet. Type the following in the Terminal screen:


`clarinet contract new nft`

### Code your smart contract

Now that we have our nft smart contract in place, let's try implementing it ☑️

Open your VS Code editor and navigate to the `contracts/nft.clar` file.

### Implement NFT Standard Trait SIP-009

Since I already mentioned in the beginning that Stacks has it's own NFT Standard SIP-009 in-place, we can just go ahead and use it into our code :v: 

If you are curious as to what this NFT Standard is -> It's basically a trait or a public interface giving us access to some functions declared in a certain way for our reference ✨

We will create a new contract for the trait by running the following command 

`clarinet contract new sip009-nft-trait`

Copy-paste the contents from [here](https://book.clarity-lang.org/ch10-01-sip009-nft-standard.html#the-sip009-nft-trait) to the `sip009-nft-trait`.You can delete the generated test file `sip009-nft-trait_test.ts`.

Now since our `nft.clar` file depends on `sip009-nft-trait.clar` file, we have to specify the same in our `Clarinet.toml` file by modifying it as follows - 
`[contracts.nft]
path = "contracts/nft.clar"
depends_on = ["sip009-nft-trait"]`

Now go ahead and add this line to the top of your `nft.clar` file

`(impl-trait .sip009-nft-trait.sip009-nft-trait)`

### Constants And Errors for NFT Smart Contract




