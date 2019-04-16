
# Exchange Guideline
API Documentation available at [Official Website](https://api.worldmobilecoin.com/)

## Installation

 1. Download wmcc-desktop [latest client](https://github.com/WorldMobileCoin/wmcc-desktop/releases) or build from [source](https://github.com/WorldMobileCoin/guideline/blob/master/BuildFromSource.md)

## Setup Wallet
1. Run wmcc-desktop client and create new account
2. Login and navigate to `Dashboard` > `Configuration` > `HTTP / RPC` > `api-key` to enter your RPC authentication, then set `no-auth` to false
3. Open your favorite browser, enter `http://[http-host]:[http-port]`, it should request for authentication. Username is `admin`
4. To open debugger, press [cmd/ctrl]+[shift]+[f12], to refresh press [cmd/ctrl]+[shift]+[f5] on wmcc-desktop client

##  Configuring Clients
You can use direct HTTP calls for invoking [REST/RPC API](http://api.worldmobilecoin.com/?javascript#configuring-clients) calls
```curl
$ curl http://x:[api-key]@[http-host]:[http-port]/

Example:
$ curl http://x:my-authentication-key@127.0.0.1:7880/ \
  -H 'Content-Type: application/json' \
  -X POST \
  --data '{ "method": "getblockchaininfo", "params": [] }'
```
OR by using [Javascript Client](http://api.worldmobilecoin.com/?javascript#using-javascript-clientLibrary ). 
 
```js
const Core = require('wmcc-core');
const Client = Core.http.Client;
const RPCClient= Core.http.RPCClient;

const client = new Client({
  network: 'mainnet',
  apiKey: '[api-key]',
  uri: 'http://[http-host]:[http-port]/'
});

const rpc = new RPCClient({
  network: 'mainnet',
  apiKey: 'my-authentication-key',
  uri: 'http://localhost:7880/'
});
```
Source: [Github](https://github.com/WorldMobileCoin/wmcc-core), HTTP/RPC commands file [rpc.js](https://github.com/WorldMobileCoin/wmcc-core/blob/master/src/http/rpc.js)

CDN:  uncompress [[wmcc-core.js]](https://cdn.jsdelivr.net/npm/wmcc-core/lib/wmcc-core.js) , compressed [[wmcc-core.min.js]](https://cdn.jsdelivr.net/npm/wmcc-core/lib/wmcc-core.min.js)

## Generate WMCC Address

### Random address
To generate new random address. Will return wmcc `address` and 256-bit `privateKey`
~~~
Method: generateaddress
Params: none
Return: Object
1. address {string}
2. privateKey {string}

Core version: +v1.1.1-beta.3
~~~
```
$ curl http://x:my-authentication-key@127.0.0.1:7880/ \
  -H 'Content-Type: application/json' \
  -X POST \
  --data '{ "method": "generateaddress", "params": [] }'
```
```js
const Core = require('wmcc-core');
const rpc = new Core.http.RPCClient({
  network: 'mainnet',
  apiKey: '[api-key]',
  uri: 'http://[http-host]:[http-port]/'
});

(async () => {
  const address = await rpc.execute('generateaddress');
  console.log(address); // return object
})().catch((err) => {
  console.error(err.stack);
});
```
### From private key
Get an `address` from existing `privateKey`:
~~~
Method: addressfromprivkey
Params: 
1. privateKey {string} - 256-bit private key
Return: Object
1. address {string}
2. privateKey {string}

Core version: +v1.1.1-beta.3
~~~
```
$ curl http://x:my-authentication-key@127.0.0.1:7880/ \
  -H 'Content-Type: application/json' \
  -X POST \
  --data '{ "method": "addressfromprivkey", "params": ["E9873D79C6D87DC0FB6A57..."] }'
```
```js
const Core = require('wmcc-core');
const rpc = new Core.http.RPCClient({
  network: 'mainnet',
  apiKey: '[api-key]',
  uri: 'http://[http-host]:[http-port]/'
});

const privKey = 'E9873D79C6D87DC0FB6A5778633389F4453213303DA61F20BD67FC233AA33262';

(async () => {
  const {address, privateKey}= await rpc.execute('addressfromprivkey', privKey);
  if (Buffer.compare(privateKey, Buffer.from(privKey, 'hex'))!== 0)
    throw new Error('Private key not match')
  console.log(address)
})().catch((err) => {
  console.error(err.stack);
});
```
## Block Notification [blocknotify]
Execute command when the best block changes
### Configuration
1. Go to `Dashboard` > `Configuration` > `Block Notification`
2. Enter executable command to `block-notify` field

Desktop version: +v1.1.1-beta.9
```
Examples:
...
curl https://[someurl.com]/blockupdate?hash="%s"
...
[path_to_exec]/blocknotify.sh --blockhash=%s
...
```

## Subscription
WMCC Desktop offered address subscription to monitor their balance. Client will execute command when subscriber address were found in block. 
Block is usually mined in 3 to 10 minutes, it should not be a problem to handle large amount of subscribers
### Configuration
1. Go to `Dashboard` > `Configuration` > `Subscription`
2. Enter executable command to `subscribe-cmd` field
3. Make sure wmcc-desktop client to have permission to execute this command

Desktop version: +v1.1.1-beta.9
```
Examples:
...
curl https://someurl.com/subscribe?address="%s"&txhash="%s"
...
/path_to_exec/subscribe.sh --address=%s --txhash=%s
...
```
### Subscribe & Unsubscribe
`addresses` parameter can be a single address or array of addresses,  number of `confirmation` argument for subscribe method only, will fire command after `n` of confirmations for listed addresses
~~~
Method: subscribe | unsubscribe
Params:  
1. addresses {array} 
2. confirmations {number} - default 0

Core version: +v1.1.1-beta.3
~~~
```curl
$ curl http://x:[api-key]@127.0.0.1:7880/ \
  -H 'Content-Type: application/json' \
  -X POST \
  --data '{
  "method": "subscribe",
  "params": [['wc1qbb3a7...', 'wc1qabyx2...', ...]]
```
```js
const Core = require('wmcc-core');
const addresses = ['wc1qbb3a7...', 'wc1qabyx2...', ...];
const rpc = new Core.http.RPCClient({
  network: 'mainnet'
});

(async () => {
  const subscribe = await rpc.execute('subscribe', addresses, 3);
  console.log(subscribe); // return true if success
  
  const unsubscribe = await rpc.execute('unsubscribe', addresses);
  console.log(unsubscribe); // return true if success
})().catch((err) => {
  console.error(err.stack);
});
```
### Subscribers
To get list of subscribers:
~~~
Method: subscribers
Params: none
Return: addresses {array}

Core version: +v1.1.1-beta.3
~~~
## Balance
### Query Address Balance
To get final balance of an address:
~~~
Method: querybalance
Params:  
1. address {string} - single address
Return:
1. amount {string} - balance amount in WMCC

Core version: +v1.1.1-beta.3
~~~
```curl
$ curl http://localhost:7880/ \
  -H 'Content-Type: application/json' \
  -X POST \
  --data '{"method": "querybalance", "params": ["wc1qttpkkjh..."]}'
```
```js
const Core = require('wmcc-core');
const rpc = new Core.http.RPCClient({
  network: 'mainnet'
});

(async () => {
  const amount= await rpc.execute('querybalance', "wc1qttpkkjh...");
  console.log(amount);
})().catch((err) => {
  console.error(err.stack);
});
```
## Transaction
These methods are involved for single address. By querying address final balance, it should be handy for exchange to calculate changes, fees and amount to withdraw.

### Create and Sign a Transaction
~~~
Method: createsigntransaction
Params:  
1. from {string} - sender address
2. privateKey {string} - from/sender privatekey
3. to {string} - receiver address
4. amount {float} - amount to transfer
5. fees {object} - can `rate` or `amount` for fee calculation
   5.1 rate {float} - using rate for fee calculation OR
   5.2 amount {float} - using `fixed` amount a.k.a hardFee
   5.3 max {float} - maximum amount of txFee, optional argument if using rate to calculate fee. Will return error if txFee exceed this amount
Return:
1. rawTransaction {string} - signed transaction

Core version: +v1.1.1-beta.3
~~~
```
$ curl http://localhost:7880/ \
  -H 'Content-Type: application/json' \
  -X POST \
  --data '{"method": "createsigntransaction",  "params": ["wc1qpg4puh3v5d2hvrusca2qgjy67uv68axu73fs5y", "3410115232eecc45e7e1809cc90f136a63f709e88ef718f27b50af282fde6d1d", "wc1qfrhm7hyr74ykcq76ltd7wyf9frdz4g9tw6jq74", 0.5, {"rate": 0.001, "max":0.01]}'
```
```js
const Core = require('wmcc-core');
const rpc = new Core.http.RPCClient({
  network: 'mainnet'
});

(async () => {
  const rawTx = await rpc.execute('createsigntransaction',
	  "wc1qpg4puh3v5d2hvrusca2qgjy67uv68axu73fs5y",
	  "3410115232eecc45e7e1809cc90f136a63f709e88ef718f27b50af282fde6d1d",
	  "wc1qfrhm7hyr74ykcq76ltd7wyf9frdz4g9tw6jq74",
	  "0.5",
	  {
		  "rate": "0.001",
		  "max": "0.01"
	  });
  console.log(rawTx);
})().catch((err) => {
  console.error(err.stack);
});
```
### Broadcast Signed Transaction
Broadcast a signed transaction:
~~~
Method: sendrawtransaction
Params:  
1. rawTransaction {string} - hex string of signed transaction
Return:
2. txHash {string} - transaction hash

Core version: +v1.0.0-beta.1
~~~
```curl
$ curl http://localhost:7880/ \
  -H 'Content-Type: application/json' \
  -X POST \
  --data '{"method": "sendrawtransaction", "params": ["01000000000101c16d71b239a173c17341b95dc5f70f27347c88a026e8a3a58f93fa23aff3917b01..."]}'
```
```js
const Core = require('wmcc-core');
const rpc = new Core.http.RPCClient({
  network: 'mainnet'
});

(async () => {
  const txHash = await rpc.execute('sendrawtransaction', "01000000000101c16d71b239a173c17341b95dc5f70f27347c88a026e8a3a58f93fa23aff3917b01...");
  console.log(txHash);
})().catch((err) => {
  console.error(err.stack);
});
```
