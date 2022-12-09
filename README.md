## BDLS protocol based PoS Blockchain

Most functionalities of this client is similar to the Ethereum golang implementation. If you do not find your question answered by the documentation, try searching the geth wiki.

## Building the source

Download the source as
```
git clone https://github.com/BDLS-bft/BDLSChain.gitt
cd BDLSChain/cmd/geth
go build .
```

Building `geth` requires both a Go (version 1.13 or later) and a C compiler. You can install
them using your favourite package manager. Once the dependencies are installed, run

```shell
make geth
```

or, to build the full suite of utilities:

```shell
make all
```

Run the testnet as:
```
./geth --testnet --rpc console
OR
./geth --testnet --verbosity 5 --rpc console

eth.blockNumber
eth.getBlock(xxxx)

Exercise the following commands:

./geth account new
./geth --testnet account new
./geth --testnet --unlock 16Fc08d853febedC8A15FC437D9760540f6F36b8  stake delegate --stake.account 16Fc08d853febedC8A15FC437D9760540f6F36b8 --stake.from 40000 --stake.to 50000
./geth --testnet --mine -unlock 0x16Fc08d853febedC8A15FC437D9760540f6F36b8 console


./geth --testnet --mine --unlock 0xF94cE232Aaa69A8285B5b93a68adb019B17Bc5BA console

eth.sendTransaction({from:"0xF94cE232Aaa69A8285B5b93a68adb019B17Bc5BA",to: "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee", value: "1888888888888888888888", data:"0xe880824e20829c40a0110bc382740be7bf0e60f65c843ffda6b9daf4034ad7cb85f887e7698340020d"})

./geth --testnet --unlock  F94cE232Aaa69A8285B5b93a68adb019B17Bc5BA  stake delegate --stake.account  F94cE232Aaa69A8285B5b93a68adb019B17Bc5BA --stake.from 40000 --stake.to 60000

eth.sendTransaction({from: "0xF94cE232Aaa69A8285B5b93a68adb019B17Bc5BA",to: "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee", value: "1888888888888888888888", data:"0xe880829c4082ea60a0939d6e7e39bd320ff535e9cb29b9c4afe415d1de8134aa4812958e00e1af40ac"})


 ./geth --testnet --mine --unlock 0xF94cE232Aaa69A8285B5b93a68adb019B17Bc5BA console 

eth.getBalance("0x16Fc08d853febedC8A15FC437D9760540f6F36b8")

eth.getBalance("0xF94cE232Aaa69A8285B5b93a68adb019B17Bc5BA")


./geth --testnet stake redeem

eth.sendTransaction({from:"0xF94cE232Aaa69A8285B5b93a68adb019B17Bc5BA",to: "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee", value: "0", data:"0xe581ff8080a00000000000000000000000000000000000000000000000000000000000000000"})


```

## Executables

The BDLS based blockchain client comes with several wrappers/executables found in the `cmd`
directory.

|    Command    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| :-----------: | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|  **`geth`**   | Our main blockchain client. It is the entry point into the network (test- for now and main- later), capable of running as a full node (default), archive node (retaining all historical state) or a light node (retrieving data live). It can be used by other processes as a gateway into the network via JSON RPC endpoints exposed on top of HTTP, WebSocket and/or IPC transports. `geth --help` for command line options.          |
|   `abigen`    | Source code generator to convert contract definitions into easy to use, compile-time type-safe Go packages. It operates on plain [Ethereum contract ABIs](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) with expanded functionality if the contract bytecode is also available. However, it also accepts Solidity source files, making development much more streamlined. Please see [Ethereum Native DApps](https://github.com/ethereum/go-ethereum/wiki/Native-DApps:-Go-bindings-to-Ethereum-contracts) wiki page for details. |
|  `bootnode`   | Stripped down version of our client implementation that only takes part in the network node discovery protocol, but does not run any of the higher level application protocols. It can be used as a lightweight bootstrap node to aid in finding peers in private networks.                                                                                                                                                                                                                                                                 |
|     `evm`     | Developer utility version of the EVM (Ethereum Virtual Machine) that is capable of running bytecode snippets within a configurable environment and execution mode. Its purpose is to allow isolated, fine-grained debugging of SVM opcodes (e.g. `evm --code 60ff60ff --debug run`).                                                                                                                                                                                                                                                                     |
| `gethrpctest` | Developer utility tool to support our [ethereum/rpc-test](https://github.com/ethereum/rpc-tests) test suite which validates baseline conformity to the [Ethereum JSON RPC](https://github.com/ethereum/wiki/wiki/JSON-RPC) specs. Please see the [Ethereum test suite's readme](https://github.com/ethereum/rpc-tests/blob/master/README.md) for details.                                                                                                                                                                                                     |
|   `rlpdump`   | Developer utility tool to convert binary RLP ([Recursive Length Prefix](https://github.com/ethereum/wiki/wiki/RLP)) dumps (data encoding used by the protocol both network as well as consensus wise) to user-friendlier hierarchical representation (e.g. `rlpdump --hex CE0183FFFFFFC4C304050583616263`).                                                                                                                                                                                                                                 |
|   `puppeth`   | a CLI wizard that aids in creating a new network.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |

## Running the Blockchain

Going through all the possible command line flags is out of scope here (please consult our
[CLI Wiki page](https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options)),
but we've enumerated a few common parameter combos to get you up to speed quickly
on how you can run your own client instance.


### Configuration

As an alternative to passing the numerous flags to the binary, you can also pass a
configuration file via:

```shell
$ geth --config /path/to/your_config.toml
```

To get an idea how the file should look like you can use the `dumpconfig` subcommand to
export your existing configuration:

```shell
$ geth --your-favourite-flags dumpconfig
```


### Programmatically interfacing `geth` nodes

As a developer, sooner rather than later you'll want to start interacting with `geth` and the
blockchain network via your own programs and not manually through the console. To aid
this, `geth` has built-in support for a JSON-RPC based APIs ([standard APIs](https://github.com/ethereum/wiki/wiki/JSON-RPC)
and [`geth` specific APIs](https://github.com/ethereum/go-ethereum/wiki/Management-APIs)).
These can be exposed via HTTP, WebSockets and IPC (UNIX sockets on UNIX based
platforms, and named pipes on Windows).

The IPC interface is enabled by default and exposes all the APIs supported by `geth`,
whereas the HTTP and WS interfaces need to manually be enabled and only expose a
subset of APIs due to security reasons. These can be turned on/off and configured as
you'd expect.

HTTP based JSON-RPC API options:

  * `--rpc` Enable the HTTP-RPC server
  * `--rpcaddr` HTTP-RPC server listening interface (default: `localhost`)
  * `--rpcport` HTTP-RPC server listening port (default: `8545`)
  * `--rpcapi` API's offered over the HTTP-RPC interface (default: `eth,net,web3`)
  * `--rpccorsdomain` Comma separated list of domains from which to accept cross origin requests (browser enforced)
  * `--ws` Enable the WS-RPC server
  * `--wsaddr` WS-RPC server listening interface (default: `localhost`)
  * `--wsport` WS-RPC server listening port (default: `8546`)
  * `--wsapi` API's offered over the WS-RPC interface (default: `eth,net,web3`)
  * `--wsorigins` Origins from which to accept websockets requests
  * `--ipcdisable` Disable the IPC-RPC server
  * `--ipcapi` API's offered over the IPC-RPC interface (default: `admin,debug,eth,miner,net,personal,shh,txpool,web3`)
  * `--ipcpath` Filename for IPC socket/pipe within the datadir (explicit paths escape it)

You'll need to use your own programming environments' capabilities (libraries, tools, etc) to
connect via HTTP, WS or IPC to a `geth` node configured with the above flags and you'll
need to speak [JSON-RPC](https://www.jsonrpc.org/specification) on all transports. You
can reuse the same connection for multiple requests!

**Note: Please understand the security implications of opening up an HTTP/WS based
transport before doing so! Hackers on the internet are actively trying to subvert
blockchain nodes with exposed APIs! Further, all browser tabs can access locally
running web servers, so malicious web pages could try to subvert locally available
APIs!**


## Contribution

Thank you for considering to help out with the source code! We welcome contributions
from anyone on the internet, and are grateful for even the smallest of fixes!


## License

The go-ethereum library (i.e. all code outside of the `cmd` directory) is licensed under the
[GNU Lesser General Public License v3.0](https://www.gnu.org/licenses/lgpl-3.0.en.html),
also included in our repository in the `COPYING.LESSER` file.

The go-ethereum binaries (i.e. all code inside of the `cmd` directory) is licensed under the
[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html), also
included in our repository in the `COPYING` file.
