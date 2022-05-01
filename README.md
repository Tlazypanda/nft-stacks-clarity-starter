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

### Visual Studio Code Extensions

- Clarity for Visual Studio code
- Rainbow brackets


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

### Name your collection

Add this line to your `nft.clar` file by replacing `name-of-your-collection` with whatever you like, can be anything 😛
I'm gonna name mine `amor-fati` 💙

`(define-non-fungible-token name-of-your-collection uint)`


### Constants And Errors for NFT Smart Contract

For our constants and errors, we have the following code - 

`;; constants
(define-constant ERR-NOT-AUTHORIZED u401)
(define-constant ERR-ALL-MINTED u101)

(define-constant CONTRACT-OWNER tx-sender)
(define-constant ITEM-COUNT u10)`

Let me explain this line-by-line 

- The not-authorised error is important for us to prevent other parties from performing certain functions that only the token holder/contract owner can perform. For example - Setting the metadata can only be done by the contract owner. 
- The all-minted error basically implies that all the assets of this collection have already been minted.
- The CONTRACT-OWNER constant basically saves the tx-sender at time of deployment to be the contract owner. 
- The ITEM-COUNT constant saves the item count to be 10 (unsigned integer) 

### Variables

For our variables, we have the following code - 

```
;; variables  
(define-data-var token-counter uint u0)  
(define-data-var base-uri (string-ascii 256) "ipfs://QmYNWaCBi2XDudU427wjvUZfV2WDBDex7myNAk2L3VGNMb/{id}.json")  
(define-data-var contract-uri (string-ascii 256) "ipfs://QmTN4THGXmYEA72ERVGZxWoLzabhjjM7dieSZ95tKri8t9")  
(define-data-var cost-per-mint uint u250000)
```

Let me explain this line-by-line 

- The token-counter just maintains a count of all the minted tokens so far.
- The base uri points to json data of the particular nft data which can be stored on decentralised storage solutions such as IPFS.
- The contract uri points to json data of the complete collection data which can be stored on decentralised storage solutions such as IPFS.
- The cost-per-mint is basically just the amount you have to pay to mint.

### Smart Contract Functions

```
;; Mint new NFT
;; can only be called from the Mint

(define-public (mint (recipient principal))
  (let (
    (count (var-get token-counter))
  )
    (asserts! (<= count ITEM-COUNT) (err ERR-ALL-MINTED))

    (try! (mint-next recipient))
    (ok true)
  )
)

(define-private (mint-next (recipient principal))
	(let
		(
			(token-id (+ (var-get token-counter) u1))
		)
		(try! (nft-mint? amor-fati token-id recipient))
        (try! (stx-transfer? u250000 tx-sender CREATOR_WALLET))
		(var-set token-counter token-id)
		(ok token-id)
	)
)
        
(define-private (is-owner (index uint) (user principal))
  (is-eq user (unwrap! (nft-get-owner? amor-fati index) false))
)        
        
(define-public (transfer (index uint) (owner principal) (recipient principal))
  (if (and (is-owner index owner) (is-owner index tx-sender))
    (match (nft-transfer? amor-fati index owner recipient)
      success (ok true)
      error (err error)
    )
    (err ERR-NOT-AUTHORIZED)
  )
)

(define-read-only (get-owner (index uint))
  (ok (nft-get-owner? amor-fati index))
)

(define-read-only (stx-balance)
  (stx-get-balance (as-contract tx-sender))
)

(define-read-only (stx-balance-of (address principal))
  (stx-get-balance address)
)

(define-read-only (get-last-token-id)
  (ok (var-get token-counter)))
  
(define-read-only (get-token-uri (id uint))
  (ok (some (var-get base-uri))))
  ```
