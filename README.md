# Description
Kekdaq is a fork of Counterparty, a protocol for the creation and use of decentralised financial
instruments such as asset exchanges, contracts for difference and dividend
payments. It uses Pepecoin as a transport layer. The contents of this
repository, `kekdaqd`, constitute the reference implementation of the
protocol.

The Counterparty protocol specification may be found at <http://counterparty.io/docs/protocol/>
and the original counterpartyd implementation at <https://github.com/CounterpartyXCP/counterpartyd>.

We provide a Docker recipe to run kekdaqd easily: <https://github.com/Kekdaq/kekdaqd-docker>.


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
Start: (must match your pepecoin.conf RPC settings, also update lib/config.py)
```
python3 ./kekdaqd.py --rpc-user=pepeuser --rpc-password=pepepass
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

Note that the syntaxes for the countpartyd and the Pepecoind configuraion
files are not the same. A Pepecoind configuration file looks like this:

	rpcuser=pepecoinrpc
	rpcpassword=PASSWORD
	testnet=1
	txindex=1
	server=1

However, a kekdaqd configuration file looks like this:

	[Default]
	pepecoind-rpc-password=PASSWORD

Note the change in hyphenation between `rpcpassword` and `rpc-password`.

If and only if kekdaqd is to be run on the Pepecoin testnet, with the
`--testnet` CLI option, Pepecoind must be set to do the same (`-testnet=1`).
kekdaqd may run with the `--testcoin` option on any blockchain,
however.

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
* All other quantities, i.e. prices, odds, leverages, feed values and target
values, fee multipliers, are specified to four decimal places.
* kekdaqd identifies an Order, Order Match or Bet Match by an
‘Order ID’, ‘Order Match ID’, or ‘Bet Match ID’, respectively. Match
IDs are concatenations of the hashes of the two transactions which compose the
corresponding Match, in the order of their appearances in the blockchain.


## Examples
The following examples are abridged for parsimony.

* Server

	The `server` command should always be running in the background. All other commands will fail if the index of the last block in the database is less than that of the last block seen by Pepecoind.

* Burn

	`burn --source=mtQheFaSfWELRB2MyMBaiWjdDm6ux9Ezns --quantity=.5`

* Send divisible or indivisible assets

	```
	send --source=mtQheFaSfWELRB2MyMBaiWjdDm6ux9Ezns --quantity=3 --asset=BBBC
	--to=n3BrDB6zDiEPWEE6wLxywFb4Yp9ZY5fHM7
	```

* Buy PEPE for KDAQ

	```
	order --source=mtQheFaSfWELRB2MyMBaiWjdDm6ux9Ezns --get-quantity=10 --get-asset=PEPE
	--give-quantity=20 --give-asset=KDAQ --expiration=10 --fee_required=.001
	```

* Buy BBBC for PEPE

	```
	order --source=mtQheFaSfWELRB2MyMBaiWjdDm6ux9Ezns --get-quantity=10 --get-asset=BBBC
	--give-quantity=20 --give-asset=PEPE --expiration=10 --fee_provided=0.001
	```

* Buy KDAQ for BBBC
	```
	order --source=mtQheFaSfWELRB2MyMBaiWjdDm6ux9Ezns --get-quantity=10 --get-asset=KDAQ
	--give-quantity=20 --give-asset=BBBC --expiration=10
	```

* BTCPay
	```
	btcpay --source=-source=mtQheFaSfWELRB2MyMBaiWjdDm6ux9Ezns --order-match-id=092f15d36786136c4d868c33356ec3c9b5a0c77de54ed0e96a8dbdd8af160c23
	```

* Issue

	`issuance --source=mtQheFaSfWELRB2MyMBaiWjdDm6ux9Ezns --quantity=100 --asset='BBBC'`

	`issuance --source=mtQheFaSfWELRB2MyMBaiWjdDm6ux9Ezns --quantity=100 --asset='BBBQ' --divisible`

* Broadcast
	```
	broadcast --source=mtQheFaSfWELRB2MyMBaiWjdDm6ux9Ezns --text="Pepecoin price feed" --value=825.22
	--fee-multiplier=0.001
	```

	Note: for some users kekdaqd has trouble parsing spaces in the `--text` argument. One workaround is to
		add an additional set of quotes. For example, `--text='"Pepecoin price feed"'`.


* Cancel
	```
	cancel --source=-source=mtQheFaSfWELRB2MyMBaiWjdDm6ux9Ezns --offer-hash=092f15d36786136c4d868c33356ec3c9b5a0c77de54ed0e96a8dbdd8af160c23
	```

* Market

	The `market` action prints out tables of open orders, open bets, feeds, and order matches currently awaiting 	        Pepecoin payments from one of your addresses.

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

