<h1 align="center">Test and Launch</h1>


## 1. Test Your Contracts on Poly Network

There are three steps help you to test and launch your contract.

> [!Note|style:flat|label:Notice]
> The premise of contract testing is that both the original chain and the target chain you want to test have been connected to the poly network, otherwise the test cannot be performed.

### Step 1. Preparation

- Deploy your **contract** in the source chain and target chain (For safety, you are recommended to complete the test in Testnet. If you have to do on Mainnet please contact [Poly Network team]( <a class="fab fa-discord" href= "https://discord.com/invite/y6MuEnq"></a>))
- Please tell the Poly Network team the "**method**" (the input parameter of cross chain function, i.e., the function of your contract called by target chain) we will add this "method" to the whitelist of relayer in order to process transaction automatically, otherwise the transaction cannot process in poly chain. 

### Step 2. Test on Poly Network
After preparation, you can try to call the cross chain function in your contract, and then you can query the steps of cross-chain transaction through [here]( https://explorer.poly.network/testnet). Analyze contract issues based on the steps the transaction reached:
- The transaction has been completed on the source chain, but not completed on Poly:
  - If the transaction has been stuck in this step for over 5 min, consider whether the input parameter "toChainId" (the input parameter of cross chain function, i.e., the target chain ID) is correct. If the "toChainId" is correct, please contact [Poly Network team]( <a class="fab fa-discord" href= "https://discord.com/invite/y6MuEnq"></a>).

> [!Note|style:flat|label:Notice] 
> The "toChainId" refers to the target chain ID registered on the poly, not the network ID. For details, see [Chain ID](https://github.com/polynetwork/docs/blob/master/config/README.md#Router-And-ChainId). Please be noted that the IDs on the Testnet and the Mainnet are different.

- The transaction hasn't been completed on the target chain:
    - Call the API getmanualtxdata with poly chain hash (the second transaction hash).

   API
    ```
    Testnet: https://bridge.poly.network/testnet/v1/getmanualtxdata
    Mainnet: https://bridge.poly.network/v1/getmanualtxdata
    ```
   Parameter
   ```
  /* @SrcChainId:    source chian id
   * @SwapTokenHash: the hahs of the cross-chain token on the source chain
   * @Hash: The token hash used to charge fees
   * @DstChainId: target chain id
   */
     ```
  Example Request
  ```bash
  curl --location --request POST 'https://bridge.poly.network/testnet/v1/getmanualtxdata' \
  --header 'Content-Type: application/json' \
  --data-raw '{
    "polyhash": "",
  }'
  ```
  Example Response
  ```json
  {
    "data": "",
    "dst_ccm": "0x"
  }
  ```
    - Get the response “data” as the input data to send transaction to “dst_ccm” on target chain.


> [!Note|style:flat|label:Notice]
> Actually, this step is to call "verifyHeaderAndExecuteTx" function of CCM on target chain. While The calling process is encapsulated in "data", so all you need to do is to send transaction with "data" to CCM on target chain.


- If you encounter an error submitting this transaction, which means there are something wrong with your contract on the target chain. Consider the following problems:
  - Make sure the “method” (the function of your contract called by target chain) is allowed to be called by CCM contract on target chain.
  - Check the “txData”(one of input parameter of cross chain function) can be parsed correctly by “method” on target chain.


- The transaction has been completed on the target chain:
    - Congratulations! You have completed the cross chain transaction.

> [!Note|style:flat|label:Notice]
> During the test, you will find that you have to send the target transactions manually. It may not be a good idea for projects. If you want to realize the automatic process, a relayer is needed. There is a [relayer solution](../../new_chain/relayer/relayer.md) for you.

### Step 3. Launch on Mainnet
After all tests are completed on the Testnet, you are ready to launch on the Mainnet. The steps are as follows:
- Firstly, deploy your contract on Mainnet;
- Secondly, tell us the "method" and the source contract hash you have deployed;
- Thirdly, start your relayer.

When all is done, you can start your cross-chain journey.
