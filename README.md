NEAR Crossword
==================

How to play with this contract
===============================
1. Clone the repo and build it with `yarn build`
2. Run `near dev-deploy` to deploy the contract to `testnet`.
3. Create a crossword, let's say that the answer to your crossword is "many clever words"
4. Answer for your crossword from now on will be a seed phrase! Let's generate key pair out of it.

   ```bash
   near generate-key randomAccountId.testnet --seedPhrase='many clever words'
   ```

   Now this key pair will be store on your machine under `~/.near-credentials/testnet/randomAccountId.json`

5. We should add your puzzle to our contract. To do that run
   
   ```bash
   near call <contract-account-id> new_puzzle '{"answer_pk":"<generated-pk>"}' --accountId=<signer-acc-id> --deposit=10
   ```
   Where:
      - `contract-account-id` - Account on which contract is stored. If you have used `near dev-deploy` in the first step it was autogenerated for you. It should look like `dev-<random-numbers>`.
      - `generate-pk` - Public key from JSON generated in the step #4
      - `accountId` - your existing testnet accountId (you can create one at https://wallet.testnet.near.org/)
      - `deposit` - reword for the person who will solve this puzzle
   
   After this call your puzzle will be added to the NEAR Crossword contract. Share your Crossword with friends, the person who will be able to solve it will be able to generate the same key pair and get the reward. Let's do that in the following steps.

6. Pretend that we have solved the puzzle and generated the very same key pair. This time it should be stored at `~/.near-credentials/testnet/<contract-id>.json`. We are using `<contract-id>` here because in the next step we will need to sign the transaction with this acc.

Atension! If you are using the same machine, your old key pair from `<dev-acc>` will be owerwriten! Save it in some other place if you need it. Keys are stored in `~/.near-credentials/testnet/` folder.

To generate the new key:
```bash
near generate-key <crossword-contract-id> --seedPhrase='many clever words'
```

Also, we need to have another key that will be used later to get the reward. Let's generate it.

```bash
near generate-key keyToGetTheReward.testnet
```

7. Let's call `submit_solution` function to solve this puzzle.

```bash
near call <contract-id> submit_solution '{"solver_pk":"<PK from keyToGetTheReward.testnet>"}' --accountId=<contract-id>
```

Puzzle solved! Let's get our reward!

8. To get the reward we need to call the `claim_reward` function with the function call key that we have added in the previous step. Before that call we should prepare the keys:

```bash
cp ~/.near-credentials/testnet/keyToGetTheReward.testnet.json ~/.near-credentials/testnet/<contract-id>.json
```

And now we can claim our reward:

```bash
near call <contract-id> claim_reward '{"receiver_acc_id":"serhii.testnet", "crossword_pk":"<PK from randomAccountId account>", "memo":"Victory!"}' --accountId=<contract-id>
```


This [React] app was initialized with [create-near-app]

Quick Start
===========

To run this project locally:

1. Prerequisites: Make sure you've installed [Node.js] ≥ 12
2. Install dependencies: `yarn install`
3. Run the local development server: `yarn dev` (see `package.json` for a
   full list of `scripts` you can run with `yarn`)

Now you'll have a local development environment backed by the NEAR TestNet!

Go ahead and play with the app and the code. As you make code changes, the app will automatically reload.

Exploring The Code
==================

1. The "backend" code lives in the `/contract` folder. See the README there for
   more info.
2. The frontend code lives in the `/src` folder. `/src/index.html` is a great
   place to start exploring. Note that it loads in `/src/index.js`, where you
   can learn how the frontend connects to the NEAR blockchain.
3. Tests: there are different kinds of tests for the frontend and the smart
   contract. See `contract/README` for info about how it's tested. The frontend
   code gets tested with [jest]. You can run both of these at once with `yarn
   run test`.

Deploy
======

Every smart contract in NEAR has its [own associated account][NEAR accounts]. When you run `yarn dev`, your smart contract gets deployed to NEAR TestNet with a throwaway account. When you're ready to make it permanent, here's how.

Step 0: Install near-cli (optional)
-------------------------------------

[near-cli] is a command line interface (CLI) for interacting with the NEAR blockchain. It was installed to the local `node_modules` folder when you ran `yarn install`, but for best ergonomics you may want to install it globally:

    yarn install --global near-cli

Or, if you'd rather use the locally-installed version, you can prefix all `near` commands with `npx`

Ensure that it's installed with `near --version` (or `npx near --version`)

Step 1: Create an account for the contract
------------------------------------------

Each account on NEAR can have at most one contract deployed to it. If you've already created an account such as `your-name.testnet`, you can deploy your contract to `crossword.your-name.testnet`. Assuming you've already created an account on [NEAR Wallet], here's how to create `crossword.your-name.testnet`:

1. Authorize NEAR CLI, following the commands it gives you:

      near login

2. Create a subaccount (replace `YOUR-NAME` below with your actual account name):

      near create-account crossword.YOUR-NAME.testnet --masterAccount YOUR-NAME.testnet

Step 2: set contract name in code
---------------------------------

Modify the line in `src/config.js` that sets the account name of the contract. Set it to the account id you used above.

    const CONTRACT_NAME = process.env.CONTRACT_NAME || 'crossword.YOUR-NAME.testnet'

Step 3: deploy!
---------------

One command:

    yarn deploy

As you can see in `package.json`, this does two things:

1. builds & deploys smart contract to NEAR TestNet
2. builds & deploys frontend code to GitHub using [gh-pages]. This will only work if the project already has a repository set up on GitHub. Feel free to modify the `deploy` script in `package.json` to deploy elsewhere.

Troubleshooting
===============

On Windows, if you're seeing an error containing `EPERM` it may be related to spaces in your path. Please see [this issue](https://github.com/zkat/npx/issues/209) for more details.


  [React]: https://reactjs.org/
  [create-near-app]: https://github.com/near/create-near-app
  [Node.js]: https://nodejs.org/en/download/package-manager/
  [jest]: https://jestjs.io/
  [NEAR accounts]: https://docs.near.org/docs/concepts/account
  [NEAR Wallet]: https://wallet.testnet.near.org/
  [near-cli]: https://github.com/near/near-cli
  [gh-pages]: https://github.com/tschaub/gh-pages
