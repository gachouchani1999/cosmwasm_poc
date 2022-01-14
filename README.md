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
INIT='{}'
```
Then we can instantiate the contract using: 
```
wasmd tx wasm instantiate $CODE_ID "$INIT" \
    --from wallet --label "awesome name service" $TXFLAG -y
```


