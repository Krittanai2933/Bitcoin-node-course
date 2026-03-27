# 🧪 Bitcoin Regtest Live Demo (bitcoin-cli)

## Assumptions

-   `bitcoind` is installed
-   Running locally (Regtest only)
-   No external peers

------------------------------------------------------------------------

## ✅ Step 0 --- Start Regtest node

``` bash
bitcoind -regtest -daemon
```

Expected:

    Bitcoin Core starting

------------------------------------------------------------------------

## ✅ Step 1 --- Check blockchain status

``` bash
bitcoin-cli -regtest getblockchaininfo
```

Expected:

``` json
{
  "chain": "regtest",
  "blocks": 0,
  "headers": 0,
  "initialblockdownload": false
}
```

------------------------------------------------------------------------

## ✅ Step 2 --- Create wallet

``` bash
bitcoin-cli -regtest createwallet "demo"
```

Expected:

``` json
{
  "name": "demo"
}
```

------------------------------------------------------------------------

## ✅ Step 3 --- Get new address

``` bash
ADDR=$(bitcoin-cli -regtest getnewaddress)
echo $ADDR
```

Expected:

    bcrt1q...

------------------------------------------------------------------------

## ✅ Step 4 --- Mine 101 blocks

``` bash
bitcoin-cli -regtest generatetoaddress 101 $ADDR
```

Expected: - Array of block hashes

------------------------------------------------------------------------

## ✅ Step 5 --- Check balance

``` bash
bitcoin-cli -regtest getbalance
```

Expected:

    50.00000000

------------------------------------------------------------------------

## ✅ Step 6 --- Create second address

``` bash
ADDR2=$(bitcoin-cli -regtest getnewaddress)
echo $ADDR2
```

------------------------------------------------------------------------

## ✅ Step 7 --- Send transaction

``` bash
TXID=$(bitcoin-cli -regtest sendtoaddress $ADDR2 10)
echo $TXID
```

Expected:

    <transaction-id>

------------------------------------------------------------------------

## ✅ Step 8 --- Check mempool

``` bash
bitcoin-cli -regtest getmempoolinfo
```

Expected:

``` json
{
  "size": 1
}
```

------------------------------------------------------------------------

## ✅ Step 9 --- Mine 1 block (confirm tx)

``` bash
bitcoin-cli -regtest generatetoaddress 1 $ADDR
```

------------------------------------------------------------------------

## ✅ Step 10 --- Check balance again

``` bash
bitcoin-cli -regtest getbalance
```

Expected:

    ~40 (plus mining reward)

------------------------------------------------------------------------

## ✅ Step 11 --- Inspect transaction

``` bash
bitcoin-cli -regtest gettransaction $TXID
```

Expected:

``` json
{
  "confirmations": 1,
  "amount": -10.00000000
}
```

------------------------------------------------------------------------

## ✅ Step 12 --- Check block height

``` bash
bitcoin-cli -regtest getblockcount
```

Expected:

    102

------------------------------------------------------------------------

## 🎯 Summary Flow

1.  Start node\
2.  Create wallet\
3.  Get address\
4.  Mine blocks\
5.  Get balance\
6.  Send transaction\
7.  Confirm transaction

------------------------------------------------------------------------

## ⚠️ Common Issues

**Insufficient funds**\
→ Mine 101 blocks first

**Wallet not loaded**

``` bash
bitcoin-cli -regtest loadwallet "demo"
```

**Port conflict**\
→ Another node already running

------------------------------------------------------------------------

## 🚀 Final Note

Regtest = full control\
You can: - Mine instantly\
- Create coins\
- Simulate full Bitcoin lifecycle locally
