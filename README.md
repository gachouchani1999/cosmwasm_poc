# cosmwasm_poc
Deploying a Counter Smart Contract on Cosmwasm Testnet Proof of Concept
## Smart Contract 
We use a very basic smart contract, the CW-template offered by Cosmwasm (1.0.0-beta) that allows for storing and reading count from a state.
```cargo generate --git https://github.com/CosmWasm/cw-template.git --name juno_stkd_poc (https://github.com/InterWasm/cw-template)```
### Environment Setup
1. Source the sandynet network configuration in terminal: `source <(curl -sSL https://raw.githubusercontent.com/CosmWasm/testnets/master/sandynet-1/defaults.env)`

2. Setup two wallets to test with: ```wasmd keys add wallet```

3. Request tokens from the faucet to be able to store and execute messages:
 ```
 JSON=$(jq -n --arg addr $(wasmd keys show -a wallet) '{"denom":"ubay","address":$addr}') && curl -X POST --header "Content-Type: application/json" --data "$JSON" https://faucet.sandynet.cosmwasm.com/credit
```

4. Export wasmd parameters to terminal to avoid repetition
```
# bash
export NODE="--node $RPC"
export TXFLAG="${NODE} --chain-id ${CHAIN_ID} --gas-prices 0.025ubay --gas auto --gas-adjustment 1.3"

# zsh
export NODE=(--node $RPC)
export TXFLAG=($NODE --chain-id $CHAIN_ID --gas-prices 0.025ubay --gas auto --gas-adjustment 1.3)
```

5. Upload Contract to blockchain and store it in env. variable $RES : 
```RES=$(wasmd tx wasm store artifacts/cw_nameservice.wasm --from wallet $TXFLAG -y --output json)```

### Instantiation
We need an Initialization message so we can store it in terminal: 
```
INIT='{"count": 11}'
```
Then we can instantiate the contract using: 
```
wasmd tx wasm instantiate $CODE_ID "$INIT" \
    --from wallet --label "awesome name service" $TXFLAG -y
```
After Instantiation we can find the smart contract address from the result: `wasm1lsmfpf5vfax77udfqwfj2fwkn28myzrqy4d34s`
We can then query the state count from: `wasmd query wasm contract-state smart $CONTRACT '{"get_count": {}}' $NODE`

### Execution
We can create a JSON of the execution message: `INCREMENT='{"increment": {}}'`

We can find from the source code the TX ID: `647183D075250EB26B670BD82EDD0B3C1A4447493436826FFE8B13D80E5EEE1C`

### Query
We can know query the count from the state of the transaction
```
COUNT_QUERY='{"get_count": {}}'
```
```wasmd query wasm contract-state smart $CONTRACT "$COUNT_QUERY" $NODE --output json```

We get a response of `{"data":{"count":12}}` showing that the count was indeed incremented by 1.
