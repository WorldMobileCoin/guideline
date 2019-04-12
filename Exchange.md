# Exchange Guideline
## Installation

 1. Download wmcc-desktop [latest client](https://github.com/WorldMobileCoin/wmcc-desktop/releases) or build from [source](https://github.com/WorldMobileCoin/guideline/blob/master/BuildFromSource.README)

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
Source: [Github](https://github.com/WorldMobileCoin/wmcc-core)
CDN:  uncompress [[wmcc-core.js]](https://cdn.jsdelivr.net/npm/wmcc-core/lib/wmcc-core.js) , compressed [[wmcc-core.min.js]](https://cdn.jsdelivr.net/npm/wmcc-core/lib/wmcc-core.min.js)

API Documentation available at [Official Website](https://api.worldmobilecoin.com/)

## Generate WMCC Address

### Random address
To generate new random address. Will return wmcc `address` and 256-bit `privateKey`
~~~
Method: generateaddress
Params: none
Return: Object
1. address {string}
2. privateKey {string}
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
~~~
Method: addressfromprivkey
Params: 
1. privateKey {string|buffer} - 256-bit private key
Return: Object
1. address {string}
2. privateKey {string|buffer}
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
2. Enter executable path to `Path` field
```
Examples:
...
curl https://someurl.com/blockupdate?hash="%s"
...
/path_to_exec/blockupdate.sh --blockhash=%s
...
```

## Subscription
WMCC Desktop offered address subscription to monitor their balance. Client will execute command when subscriber address were found in block. 
Block is usually mined in 5 - 30 minutes, it should not be a problem to handle large amount of subscribers
### Configuration
1. Go to `Dashboard` > `Configuration` > `Subscription`
2. Enter executable path to `Path` field
3. Make sure wmcc-desktop client to have permission to execute this command
```
Examples:
...
curl https://someurl.com/subscribe?address="%s"&txhash="%s"
...
/path_to_exec/subscribe.sh --address=%s --txhash=%s
...
```
### Subscribe & Unsubscribe
`addresses` parameter can be a single address or array of addresses, `confirmation` and `includevin` for subscribe method only

~~~
Method: subscribe | unsubscribe
Params:  
1. addresses {array|string} 
2. confirmations {number} - default 0
3. includevin {boolean} - default false
~~~
```curl
$ curl http://x:[api-key]@127.0.0.1:7880/ \
  -H 'Content-Type: application/json' \
  -X POST \
  --data '{
  "method": "subscribe",
  "params": [['wc1qbb3a7...', 'wc1qabyx7...', ...]]
```
```js
const Core = require('wmcc-core');
const addresses = ['wc1qbb3a7...', 'wc1qabyx7...', ...];
const rpc = new Core.http.RPCClient({
  network: 'mainnet'
});

(async () => {
  const subscribe = await rpc.execute('subscribe', addresses, 0, false);
  console.log(subscribe); // return true if success
  
  const unsubscribe = await rpc.execute('unsubscribe', addresses);
  console.log(unsubscribe); // return true if success
})().catch((err) => {
  console.error(err.stack);
});
```
### Subscribers
To get list of subscribers
~~~
Method: subscribers
Params: none
Return: addresses {array}
~~~
## Transaction
These methods are involved for single address. By querying address final balance, it should be handy for exchange to calculate changes, fees and amount to withdraw.
### Query Address Balance
### Create, Sign and Send a Transaction
