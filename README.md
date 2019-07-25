# Description
Kekdaq is a heavily modified fork of Counterparty, a protocol for the creation and 
use of decentralised instruments such as trading cards, unique digital items, tokens, 
smart contracts, peer-to-peer non-custodial exchange, digital assets, and colored 
coins. Users control their own wallets and assets without a trusted third party,
through the PepeCoin / Memetic Project blockchain.

It uses Pepecoind (aka Memetic) as a transport layer. The contents of this
repository, `kekdaqd`, constitute the reference implementation of the
protocol. 

Kekdaq is modified from the original Counterparty protocol to be compliant with the
rules and regulations of the USA. It is fully non-custodial and all "betting" and "dividend" 
features are disabled or removed. There are additional features being added and the Counterparty
protocol docs will eventually be replaced with a concise Kekdaq protocol specification.

The original Counterparty protocol specification may be found at <http://counterparty.io/docs/protocol/>
and the original counterpartyd implementation at <https://github.com/CounterpartyXCP/counterpartyd>.

We provide a bash script to install and run kekdaqd easily and a Docker script is on the roadmap.

# Dependencies
* [Python 3](http://python.org)
* Python 3 packages: apsw, requests, appdirs, prettytable, python-dateutil, json-rpc, tornado, flask, Flask-HTTPAuth, pycoin, pyzmq(v2.2+), pycrypto (see [this link](https://github.com/Kekdaq/kekdaqd/blob/master/pip-requirements.txt) for exact working versions)
* pepecoind

# Quick Start
```
sudo apt install python3 python3-pip
git clone https://github.com/kekdaq/kekdaqd
cd kekdaqd
pip3 install --upgrade -r pip-requirements.txt
```
Config: User settings are loaded from kekdaqd.conf or ~/.kekdaq/kekdaqd/kekdaqd.conf
        Program settings are loaded from lib/config.py

Start: Set rpc user/pass in kekdaqd.conf or on the command line and start kekdaqd.py:
```
python3 ./kekdaqd.py --rpc-user=pepeuser --rpc-password=peperpcpass
```

# Installation

**NOTE: This section covers manual installation of kekdaqd. If you want more of
an automated approach to kekdaqd installation for Windows and Linux, see [this link](http://kekdaqd-build.readthedocs.org/en/latest/).**

In order for kekdaqd to function, it must be able to communicate with a
running instance of Pepecoind or Pepecoin-Qt, which handles many Pepecoin‐specific
matters on its behalf, including all wallet and private key management. For
such interoperability, Pepecoind must be run with the following options:
`-txindex=1` `-server=1`. This may require the setting of a JSON‐RPC password,
which may be saved in Pepecoind’s configuration file.

kekdaqd needs to know at least the JSON‐RPC password of the Pepecoind with
which it is supposed to communicate. The simplest way to set this is to
include it in all command‐line invocations of kekdaqd, such as
`./kekdaqd.py --rpc-password=PASSWORD ACTION`. To make this and other
options persistent across kekdaqd sessions, one may store the desired
settings in a configuration file specific to kekdaqd.

The syntaxes for the countpartyd and the Pepecoind configuration
files are not the same. A Pepecoind configuration file looks like this:

	rpcuser=pepecoinrpc
	rpcpassword=PASSWORD
	testnet=1
	txindex=1
	server=1

A kekdaqd configuration file looks like this:

	[Default]
	backend-rpc-user=pepeuser
	backend-rpc-password=peperpcpass
	rpc-user=pepecoinrpc
	rpc-password=PASSWORD
	

Note that backend-rpc-user is the user for the kekdaqd API and rpc-user is the user for the pepecoin daemon RPC.

Insight API is required and pepe-insight-api repo is provided with default configs that match kekdaqd defaults. 
Setup scripts under development now will help manage the interworking components.

# Updating your requirements

Sometimes the underlying package requirements may change for `kekdaqd`. If you build and installed it from scratch,
you can manually update these requirements by executing something like:

    ```pip install --upgrade -r pip-requirements.txt```

# Test suite

The test suite is invoked with `py.test` in the root directory of the repository.
Pepecoind testnet and mainnet must run on the default ports and use the same rpcuser and rpcpassword.
Do not include the following values in kekdaqd.conf: pepecoind-rpc-connect, bitcoind-rpc-port, rpc-host, rpc-port and testnet.

# Usage
The command‐line syntax of kekdaqd is generally that of
`./kekdaqd.py {OPTIONS} ACTION {ACTION-OPTIONS}`. There is a one action
per message type, which action produces and broadcasts such a message; the
message parameters are specified following the name of the message type. There
are also actions which do not correspond to message types, but rather exist to
provide information about the state of the Counterparty network, e.g. current
balances or open orders.

For a summary of the command‐line arguments and options, see
`./kekdaqd.py --help`.

# Versioning
* Major version changes require a full rebuild of the database.
* Minor version changes require a database reparse.
* All protocol changes are retroactive on testnet.

## Input and Output
* Quantities of divisible assets are written to eight decimal places.
* Quantities of indivisible assets are written as integers.
* All other quantities, i.e. prices, feed values and target
values, fee multipliers, are specified to four decimal places.
* kekdaqd identifies an Order, Order Match or Bet Match by an
‘Order ID’, ‘Order Match ID’, or ‘Bet Match ID’, respectively. Match
IDs are concatenations of the hashes of the two transactions which compose the
corresponding Match, in the order of their appearances in the blockchain.


## How to Use / Examples

* Server

	The `server` command should always be running in the background. 
	A second kekdaqd or kekdaq-wallet instance is used to communicate with the kekdaqd server.
	Other commands will fail if kekdaqd's block height falls behind pepecoind.

* Burn 
	Burn PEPE/MEME for KDAQ at 1:10 ratio before block 3,000,000.
	```
	./kekdaq.py burn --source=PCLhE3kedPr5eavvbt8dBkRFm3ozgEcmaB --quantity=100
	```

* Send divisible or indivisible assets

	```
	./kekdaq.py send --source=PCLhE3kedPr5eavvbt8dBkRFm3ozgEcmaB --quantity=3 --asset=PEPECARD
	--to=PEPHiyjtNwJcyq4rJQSRDanPfS9hVkWSNB
	```

* Buy PEPE for KDAQ

	```
	order --source=PCLhE3kedPr5eavvbt8dBkRFm3ozgEcmaB --get-quantity=10 --get-asset=PEPE
	--give-quantity=20 --give-asset=KDAQ --expiration=10 --fee_required=.001
	```

* Buy PEPECARD for PEPE

	```
	order --source=PCLhE3kedPr5eavvbt8dBkRFm3ozgEcmaB --get-quantity=10 --get-asset=PEPECARD
	--give-quantity=20 --give-asset=PEPE --expiration=10 --fee_provided=0.001
	```

* Buy KDAQ for PEPECARD
	```
	order --source=PCLhE3kedPr5eavvbt8dBkRFm3ozgEcmaB --get-quantity=1000 --get-asset=KDAQ
	--give-quantity=2 --give-asset=PEPECARD --expiration=10
	```

* PepePay
	```
	pepepay --source=-source=PCLhE3kedPr5eavvbt8dBkRFm3ozgEcmaB --order-match-id=092f15d36786136c4d868c33356ec3c9b5a0c77de54ed0e96a8dbdd8af160c23
	```

* Issue Non Divisible assets (like cards)

	`issuance --source=PCLhE3kedPr5eavvbt8dBkRFm3ozgEcmaB --quantity=1000 --asset='MYNEWASSET'`

* Issue divisible assets

	`issuance --source=PCLhE3kedPr5eavvbt8dBkRFm3ozgEcmaB --quantity=1000 --asset='BBBQ' --divisible`

* Broadcast
	```
	broadcast --source=PCLhE3kedPr5eavvbt8dBkRFm3ozgEcmaB --text="Pepecoin price feed" --value=825.22
	--fee-multiplier=0.001
	```

	Note: for some users kekdaqd has trouble parsing spaces in the `--text` argument. One workaround is to
		add an additional set of quotes. For example, `--text='"Pepecoin price feed"'`.


* Cancel
	```
	cancel --source=-source=PCLhE3kedPr5eavvbt8dBkRFm3ozgEcmaB --offer-hash=092f15d36786136c4d868c33356ec3c9b5a0c77de54ed0e96a8dbdd8af160c23
	```

* Market

	The `market` action prints out tables of open orders, feeds, and order matches currently awaiting Pepecoin payments from one of your addresses.	
	
	It is capable of filtering orders by assets to be bought and sold.

	Example:

	To filter the market to only show offers to sell (give) PEPE:
	```
	market --give-asset=PEPE
	```

	To filter the market to only show offers to buy (get) PEPE:
	```
	market --get-asset=PEPE
	```

	To filter the market to only show offers to sell PEPE for KDAQ:
	```
	market --give-asset=PEPE --get-asset=KDAQ
	```

* Asset

	The `asset` action displays the basic properties of a given asset.

* Address

	The `address` action displays the details of of all transactions involving the Counterparty address which is its argument.




#
#

---------------------
Copyright (c) 2016-2019 PepeCoin / Memetic Developers
Copyright (c) 2013-2018 Counterparty Developers

Released under MIT License

