# Honey_pot-Lottery
This is one kind of Lottery system.
Here's how it works:
- Players pay an entry fee of 0.05 $SOL for a golden ticket.
- 0.01 $SOL is taken from this fee and deposited into the community treasury 
- Players stake their golden ticket for the duration of the Pot.
- When the Pot ends, the wallet containing the winning ticket receives the contents of the pot.
- 10% of every pot is deposited into the community treasury. There are daily, weekly, and monthly pots.
- Over time, we will add pots with different ticket prices, time limits, and rules for entry

## Install Dependencies
- Install `node` and `yarn`
- Install `ts-node` as global command
- Confirm the solana wallet preparation: `/home/fury/.config/solana/id.json` in test case


### Before that in your PC solana programs have to be installed with this version
- $ solana --version   // solana-cli 1.8.16 (src:23af37fe; feat:1886190546)
- $ anchor --version   // anchor-cli 0.20.1  //use avm to install and use desired version: Refer [here](https://book.anchor-lang.com/getting_started/installation.html?highlight=avm#installing-using-anchor-version-manager-avm-recommended) 
```powershell
cargo install --git https://github.com/project-serum/anchor avm --locked --force
```

- $ node --version   // v16.14.0
- $ yarn --version   // 1.22.17  // use 
```powershell
npm install --global yarn@1.22.17
```

- $ cargo --version // cargo 1.59.0 (49d8809dc 2022-02-10)//`rustup install 1.59.0` and then if needed `rustup override set 1.59.0`

## Usage
- Main script source for all functionality is here: `/cli/script.ts`
- Program account types are declared here: `/cli/types.ts`
- Idl to make the JS binding easy is here: `/cli/honey_pot.json`

Able to test the script functions working in this way.
- Change commands properly in the main functions of the `script.ts` file to call the other functions
- Confirm the `ANCHOR_WALLET` environment variable of the `ts-node` script in `package.json`
- Run `yarn ts-node`


## How to deploy this program?
First of all, you have to git clone in your PC.
In the folder `Lottery_DAO/honey_pot`, in the terminal 
1. `yarn`
2. `anchor build`
In the last sentence you can see:  
```powershell
 To deploy this program:    
 $ solana program deploy /home/ubuntu/apollo/Lottery_DAO/honey_pot/target/deploy/honey_pot.so  
 The program address will default to this keypair (override with --program-id):    
 /home/ubuntu/apollo/Lottery_DAO/honey_pot/target/deploy/honey_pot-keypair.json
```  
3. `solana-keygen pubkey /home/ubuntu/apollo/Lottery_DAO/honey_pot/target/deploy/honey_pot-keypair.json`
4. You can get the pubkey of the program ID : ex."CKy...Q98F"
5. Please add this pubkey to the lib.rs
  `line 15: declare_id!("CKy...Q98F");`
6. Please add this pubkey to the Achor.toml
  `line 2: upgrade = "CKy...Q98F"`
7. Please add this pubkey to the init.ts
  `line 36: const PROGRAM_ID = "CKy...Q98F";`
8. `anchor build` again
9. `solana program deploy /home/ubuntu/apollo/Lottery_DAO/honey_pot/target/deploy/honey_pot.so`
10. In the scripts.ts code, `line 39`, check the network.(`devnet` or `mainnet-beta`)
11. In the scripts.ts code, `line 71`, de-comment `await initProject(payer.publicKey);`
12. `yarn ts-node`
13. If this error comes - `Error: Provider local is not available on browser.`, `export BRWOSER=`
14. `yarn ts-node`


Then, you can enjoy this program with new program_id.

## Features

### As a Smart Contract Owner
For the first time use, the Smart Contract Owner should `initialize` the Smart Contract for global account allocation.
```js
/**
 * @dev Before use this program, the accounts have to be initialized
 * @param userAddress : The caller who want to init the project
 * @returns 
 */
export const initProject = async (
    userAddress: PublicKey,
)
```

### As a user
All users can buy tickets by calling the functions with amount of tickets.
```js
/**
 * @dev Buy daily tickets function
 * @param userAddress The caller of this function- the player of the game
 * @param amount The amount of tickets that the caller bought
 */
export const buyTicket = async (
    userAddress: PublicKey,
    amount: number
)
```
before `buyTicket`, initialze the userPool.
```js
/**
 * @dev Create account of users with the identifier, timestamp, seed
 * @param userAddress The caller of this function - the player of the game
 * @param identifier The count of the dailyPot
 * @param timestamp The startTime of the dailyPot
 */
export const initIdPool = async (
    userAddress: PublicKey,
    identifier: number,
    timestamp: number,
) 
```


After the lottery ends, they can reveal the winner by calling function.
```js
/**
 * @dev The function which can reveal the winner of the daily_pot
 * @param userAddress The caller address
 */
export const revealWinner = async (
    userAddress: PublicKey,
): Promise<PublicKey>
```

Then, the winner can claim reward from the reward pool by calling funciton.
```js
/**
 * @dev The claim funtion that can claim the winner Prize from the daily_pot
 * @param userAddress The caller address to claim reward from the rewardVault
 */
export const claim = async (
    userAddress: PublicKey,
)
```

Above 3 functions are implemented for 3 pots- daily, weekly, and monthly pots.

- Buyfunction
```js
/**
 * @dev Buy Weekly tickets function
 * @param userAddress The caller of this function- the player of the game
 * @param amount The amount of tickets that the caller bought
 */
export const buyWeeklyTicket = async (
    userAddress: PublicKey,
    amount: number
) 
```

```js
/**
 * @dev Buy Monthly tickets function
 * @param userAddress The caller of this function- the player of the game
 * @param amount The amount of tickets that the caller bought
 */
export const buyMonthlyTicket = async (
    userAddress: PublicKey,
    amount: number
)
```


- RevealFunction
```js
/**
 * @dev The function which can reveal the winner of the weekly_pot
 * @param userAddress The caller address
 */
export const revealWeeklyWinner = async (
    userAddress: PublicKey,
): Promise<PublicKey>
```
```js
/**
 * @dev The function which can reveal the winner of the monthly_pot
 * @param userAddress The caller address
 */

export const revealMonthlyWinner = async (
    userAddress: PublicKey,
): Promise<PublicKey> 
```

- ClaimFunction
```js
/**
 * @dev The claim funtion that can claim the winner Prize from the weekly_pot
 * @param userAddress The caller address to claim reward from the rewardVault
 */
export const claimWeekly = async (
    userAddress: PublicKey,
)
```
```js
/**
 * @dev The claim funtion that can claim the winner Prize from the weekly_pot
 * @param userAddress The caller address to claim reward from the rewardVault
 */
export const claimWeekly = async (
    userAddress: PublicKey,
)
```
