# Safe demo box

This project contains examples how to use the Gnosis Safe and the services related to it.

**All Safes are created and used on Rinkeby**

## Relay service interaction demo
* start ganache with `ganache-cli -s 42` (accounts will be used for signing)

### Safe deployment
* trigger Safe deployment
```bash
curl -X POST \
  https://safe-relay.rinkeby.gnosis.pm/api/v1/safes/ \
  -H 'Content-Type: application/json' \
  -d '{
	"owners": ["0x39cBD3814757Be997040E51921e8D54618278A08"],
	"threshold": "1",
	"s": "42424242424242424242"
}'
```
* copy `safe` address and fund it with the required `payment` (on **Rinkeby**)
* notify the service that the safe was funded
```bash
curl -X PUT \
  https://safe-relay.rinkeby.gnosis.pm/api/v1/safes/<safe_address_checksummed>/funded/
```
* query creation state
```bash
curl -X GET \
  https://safe-relay.rinkeby.gnosis.pm/api/v1/safes/<safe_address_checksummed>/funded/
```

### Transaction execution
* retrieve estimate
```bash
curl -X POST \
  https://safe-relay.rinkeby.gnosis.pm/api/v1/safes/<safe_address_checksummed>/transactions/estimate/ \
  -H 'Content-Type: application/json' \
  -d '{
	"to": "0x39cBD3814757Be997040E51921e8D54618278A08",
	"value": "10000000000000000",
	"operation": "0"
}'
```
* generate signature using the [demo.js](./scripts/demo.js)
```bash
truffle console
require("./scripts/demo.js")(web3, "<safe_address_checksummed>", "0x39cBD3814757Be997040E51921e8D54618278A08", "0x39cBD3814757Be997040E51921e8D54618278A08")
```
* trigger transaction
```bash
curl -X POST \
  https://safe-relay.rinkeby.gnosis.pm/api/v1/safes/<safe_address_checksummed>/transactions/ \
  -H 'Content-Type: application/json' \
  -d '{
	"to": "0x39cBD3814757Be997040E51921e8D54618278A08",
	"value": "10000000000000000",
	"operation": "0",
	"safeTxGas": 42671,
    "dataGas": 40660,
    "gasPrice": 10000000000,
    "nonce": 0,
    "signatures": [{
    	"r": "<demo_script_r>",
    	"s": "<demo_script_s>",
    	"v": <demo_script_v>
    }]
}'
```
