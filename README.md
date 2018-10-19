## Go Usechain

Official golang implementation of the Usechain.

[![API Reference](
https://camo.githubusercontent.com/915b7be44ada53c290eb157634330494ebe3e30a/68747470733a2f2f676f646f632e6f72672f6769746875622e636f6d2f676f6c616e672f6764646f3f7374617475732e737667
)](https://godoc.org/github.com/usechain/go-usechain)
[![Go Report Card](https://goreportcard.com/badge/github.com/usechain/go-usechain)](https://goreportcard.com/report/github.com/usechain/go-usechain)
[![Travis](https://travis-ci.org/usechain/go-usechain.svg?branch=master)](https://travis-ci.org/usechain/go-usechain)
[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/usechain?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

The source files are based on [go-ethereum](https://github.com/ethereum/go-ethereum), thanks to Ethereum authors.

Many documents you can reference to go-ethereum.

Automated builds are available for stable releases and the unstable master branch.
Binary archives are published at https://github.com/usechain/go-usechain/releases .

## Building the source

Building usechain requires both a Go (version 1.7 or later) and a C compiler.
You can install them using your favourite package manager.
Once the dependencies are installed, run

    make used

or, to build the full suite of utilities:

    make all

### Programatically interfacing usechain nodes

As a developer, sooner rather than later you'll want to start interacting with usechain and the usechain
network via your own programs and not manually through the console. To aid this, usechain has built in
support for a JSON-RPC based APIs . These can be
exposed via HTTP, WebSockets and IPC (unix sockets on unix based platforms, and named pipes on Windows).

The IPC interface is enabled by default and exposes all the APIs supported by usechain, whereas the HTTP
and WS interfaces need to manually be enabled and only expose a subset of APIs due to security reasons.
These can be turned on/off and configured as you'd expect.

HTTP based JSON-RPC API options:

  * `--rpc` Enable the HTTP-RPC server
  * `--rpcaddr` HTTP-RPC server listening interface (default: "localhost")
  * `--rpcport` HTTP-RPC server listening port (default: 8545)
  * `--rpcapi` API's offered over the HTTP-RPC interface (default: "eth,net,web3")
  * `--rpccorsdomain` Comma separated list of domains from which to accept cross origin requests (browser enforced)
  * `--ws` Enable the WS-RPC server
  * `--wsaddr` WS-RPC server listening interface (default: "localhost")
  * `--wsport` WS-RPC server listening port (default: 8546)
  * `--wsapi` API's offered over the WS-RPC interface (default: "eth,net,web3")
  * `--wsorigins` Origins from which to accept websockets requests
  * `--ipcdisable` Disable the IPC-RPC server
  * `--ipcapi` API's offered over the IPC-RPC interface (default: "admin,debug,eth,miner,net,personal,shh,txpool,web3")
  * `--ipcpath` Filename for IPC socket/pipe within the datadir (explicit paths escape it)

You'll need to use your own programming environments' capabilities (libraries, tools, etc) to connect
via HTTP, WS or IPC to a usechain node configured with the above flags and you'll need to speak [JSON-RPC](http://www.jsonrpc.org/specification)
on all transports. You can reuse the same connection for multiple requests!

**Note: Please understand the security implications of opening up an HTTP/WS based transport before
doing so! Hackers on the internet are actively trying to subvert usechain nodes with exposed APIs!
Further, all browser tabs can access locally running webservers, so malicious webpages could try to
subvert locally available APIs!**

### Operating a private network

Maintaining your own private network is more involved as a lot of configurations taken for granted in
the official networks need to be manually set up.

#### Defining the private genesis state

First, you'll need to create the genesis state of your networks, which all nodes need to be aware of
and agree upon. This consists of a  JSON file (e.g. call it `genesis.json`, you can find it in `go-usechain/build/config/`):

```json
{
    "nonce":"0x0000000000000042",
    "mixhash":"0x0000000000000000000000000000000000000000000000000000000000000000",
    "difficulty": "0x4000",
    "alloc": {
        "0xfffffffffffffffffffffffffffffffff0000001": {
            "balance": "1000000000000000000000000000",
            "code": "0x6080604052600436106101745763ffffffff7c01000000000000000000000000000000000000000000000000000000006000350416630c822a53811461017957806310c956ea146101b15780631bb2a3151461029f57806323d39e92146103065780633029a8f61461032757806342077a38146103485780636399cb6c1461036f5780637120bb861461038457806372884eb2146103b857806375d0c0dc1461054057806385a4550f146105ca5780638c9540d3146105e25780639b629b4b1461061e578063a0a8e4601461078e578063ac1612a1146107a3578063ae386861146107bb578063b19a9ff214610898578063b1a0ad99146108ad578063b42e0f85146108c2578063b7563930146108da578063c03c1796146108ef578063c8f3e94a1461090e578063cacc934c14610949578063d521b8c514610a1e578063d8aad87b14610a3f578063dfac1c4d14610a54578063ec7caf4314610a6c578063f40d30ee14610a81578063fdf03f8614610aa2575b600080fd5b34801561018557600080fd5b5061019d600435600160a060020a0360243516610b77565b604080519115158252519081900360200190f35b3480156101bd57600080fd5b506040805160206004803580820135601f810184900484028501840190955284845261028694369492936024939284019190819084018382808284375050604080516020601f89358b018035918201839004830284018301909452808352979a99988101979196509182019450925082915084018382808284375050604080516020601f89358b018035918201839004830284018301909452808352979a999881019791965091820194509250829150840183828082843750949750610b979650505050505050565b6040805192835290151560208301528051918290030190f35b3480156102ab57600080fd5b5060408051602060046024803582810135601f810185900485028601850190965285855261019d958335600160a060020a0316953695604494919390910191908190840183828082843750949750610bc19650505050505050565b34801561031257600080fd5b5061019d600160a060020a0360043516610cba565b34801561033357600080fd5b5061019d600160a060020a0360043516610d0c565b34801561035457600080fd5b5061035d610d2a565b60408051918252519081900360200190f35b34801561037b57600080fd5b5061035d610d30565b34801561039057600080fd5b5061039c600435610d36565b60408051600160a060020a039092168252519081900360200190f35b3480156103c457600080fd5b506103d9600160a060020a0360043516610d5e565b6040518087151515158152602001861515151581526020018560ff1660ff168152602001806020018060200180602001848103845287818151815260200191508051906020019080838360005b8381101561043e578181015183820152602001610426565b50505050905090810190601f16801561046b5780820380516001836020036101000a031916815260200191505b50848103835286518152865160209182019188019080838360005b8381101561049e578181015183820152602001610486565b50505050905090810190601f1680156104cb5780820380516001836020036101000a031916815260200191505b50848103825285518152855160209182019187019080838360005b838110156104fe5781810151838201526020016104e6565b50505050905090810190601f16801561052b5780820380516001836020036101000a031916815260200191505b50995050505050505050505060405180910390f35b34801561054c57600080fd5b50610555610f3a565b6040805160208082528351818301528351919283929083019185019080838360005b8381101561058f578181015183820152602001610577565b50505050905090810190601f1680156105bc5780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b3480156105d657600080fd5b5061039c600435610f9a565b3480156105ee57600080fd5b50610603600160a060020a0360043516610fa8565b60408051921515835290151560208301528051918290030190f35b34801561062a57600080fd5b5061063f600160a060020a0360043516610fc6565b6040518085151515158152602001806020018060200180602001848103845287818151815260200191508051906020019080838360005b8381101561068e578181015183820152602001610676565b50505050905090810190601f1680156106bb5780820380516001836020036101000a031916815260200191505b50848103835286518152865160209182019188019080838360005b838110156106ee5781810151838201526020016106d6565b50505050905090810190601f16801561071b5780820380516001836020036101000a031916815260200191505b50848103825285518152855160209182019187019080838360005b8381101561074e578181015183820152602001610736565b50505050905090810190601f16801561077b5780820380516001836020036101000a031916815260200191505b5097505050505050505060405180910390f35b34801561079a57600080fd5b50610555611196565b3480156107af57600080fd5b5061035d6004356111cd565b3480156107c757600080fd5b5060408051602060046024803582810135601f810185900485028601850190965285855261035d95833560ff1695369560449491939091019190819084018382808284375050604080516020601f89358b018035918201839004830284018301909452808352979a99988101979196509182019450925082915084018382808284375050604080516020601f89358b018035918201839004830284018301909452808352979a9998810197919650918201945092508291508401838280828437509497506111ec9650505050505050565b3480156108a457600080fd5b5061035d61132b565b3480156108b957600080fd5b5061035d611331565b3480156108ce57600080fd5b5061039c600435611337565b3480156108e657600080fd5b5061035d611345565b3480156108fb57600080fd5b5061090c600435602435151561134b565b005b34801561091a57600080fd5b506109266004356114a0565b604080519215158352600160a060020a0390911660208301528051918290030190f35b34801561095557600080fd5b506040805160206004803580820135601f810184900484028501840190955284845261028694369492936024939284019190819084018382808284375050604080516020601f89358b018035918201839004830284018301909452808352979a99988101979196509182019450925082915084018382808284375050604080516020601f89358b018035918201839004830284018301909452808352979a9998810197919650918201945092508291508401838280828437509497506114c59650505050505050565b348015610a2a57600080fd5b5061019d600160a060020a03600435166114d3565b348015610a4b57600080fd5b5061035d611618565b348015610a6057600080fd5b5061039c60043561161e565b348015610a7857600080fd5b5061035d61162c565b348015610a8d57600080fd5b50610555600160a060020a0360043516611632565b348015610aae57600080fd5b506040805160206004803580820135601f810184900484028501840190955284845261019d94369492936024939284019190819084018382808284375050604080516020601f89358b018035918201839004830284018301909452808352979a99988101979196509182019450925082915084018382808284375050604080516020601f89358b018035918201839004830284018301909452808352979a9998810197919650918201945092508291508401838280828437509497506116cd9650505050505050565b600b60209081526000928352604080842090915290825290205460ff1681565b60008080610ba8815b8787876111ec565b90508015610bb857806001925092505b50935093915050565b3360008181526007602052604081205490919060ff161515610be257600080fd5b600160a060020a03841660009081526007602052604090205460ff1615610c0857600080fd5b600160a060020a0384166000908152600760209081526040808320805460ff19166001179055600982529091208451610c4392860190611a79565b506008805460018101825560008281527ff3f7a9fe364faab93b216da50a3214154f22a0a2b415b23a84c8169e8b636ee3909101805473ffffffffffffffffffffffffffffffffffffffff1916600160a060020a0388161790555490541115610cab57600080fd5b610cb36117ce565b5092915050565b600160a060020a0381166000908152600e602052604081205460ff161515600114610ce457600080fd5b50600160a060020a0381166000908152600e6020526040902054610100900460ff165b919050565b600160a060020a03166000908152600c602052604090205460ff1690565b60065481565b60045481565b600d805482908110610d4457fe5b600091825260209091200154600160a060020a0316905081565b600e60209081526000918252604091829020805460018083018054865160026101009483161585026000190190921691909104601f810187900487028201870190975286815260ff8085169793850481169662010000909504169493909290830182828015610e0e5780601f10610de357610100808354040283529160200191610e0e565b820191906000526020600020905b815481529060010190602001808311610df157829003601f168201915b50505060028085018054604080516020601f6000196101006001871615020190941695909504928301859004850281018501909152818152959695945090925090830182828015610ea05780601f10610e7557610100808354040283529160200191610ea0565b820191906000526020600020905b815481529060010190602001808311610e8357829003601f168201915b5050505060038301805460408051602060026001851615610100026000190190941693909304601f8101849004840282018401909252818152949594935090830182828015610f305780601f10610f0557610100808354040283529160200191610f30565b820191906000526020600020905b815481529060010190602001808311610f1357829003601f168201915b5050505050905086565b606060405190810160405280602481526020017f557365636861696e20436572746966696361746520536d61727420436f6e747281526020017f6163742e0000000000000000000000000000000000000000000000000000000081525081565b6008805482908110610d4457fe5b60076020526000908152604090205460ff8082169161010090041682565b600c602090815260009182526040918290208054600180830180548651600261010094831615949094026000190190911692909204601f810186900486028301860190965285825260ff90921694929390929083018282801561106a5780601f1061103f5761010080835404028352916020019161106a565b820191906000526020600020905b81548152906001019060200180831161104d57829003601f168201915b50505060028085018054604080516020601f60001961010060018716150201909416959095049283018590048502810185019091528181529596959450909250908301828280156110fc5780601f106110d1576101008083540402835291602001916110fc565b820191906000526020600020905b8154815290600101906020018083116110df57829003601f168201915b5050505060038301805460408051602060026001851615610100026000190190941693909304601f810184900484028201840190925281815294959493509083018282801561118c5780601f106111615761010080835404028352916020019161118c565b820191906000526020600020905b81548152906001019060200180831161116f57829003601f168201915b5050505050905084565b60408051808201909152600781527f56657220302e3100000000000000000000000000000000000000000000000000602082015281565b60118054829081106111db57fe5b600091825260209091200154905081565b600254336000908152600e602090815260409091208054600160ff19909116811762ff000019166201000060ff8a1602178255865161123393919092019190870190611a79565b50336000908152600e60209081526040909120845161125a92600290920191860190611a79565b50336000908152600e60209081526040909120835161128192600390920191850190611a79565b506000818152600a6020526040808220805474ffffffffffffffffffffffffffffffffffffffff001916336101008102919091179091556002805460019081019091556011805480830182558186527f31ecc21a745e3968a04e9570e4425bc18fa8019c68028196b546d1669c200c6801869055546006559151919284927f3c7e93ea9c1a75278519c8147f1e5efc58a04997a53c8f83d4692421a1c31f919190a4949350505050565b60035481565b60005481565b6010805482908110610d4457fe5b60015481565b3360008181526007602052604090205460ff16151561136957600080fd5b6000838152600a6020526040902054839060ff161561138757600080fd5b83151561139357600080fd5b82151561143e576000848152600b602090815260408083203384528252808320805460ff19169055868352600a8252808320546101009004600160a060020a03168352600e9091528120805462ffffff19168155906113f56001830182611af7565b611403600283016000611af7565b611411600383016000611af7565b50506000848152600a60205260409020805474ffffffffffffffffffffffffffffffffffffffffff191690555b6000848152600b60209081526040808320338085529252808320805460ff19166001908117909155905190928792917fe23883812358b89dbfe01a5d592aed112fb4ad222addc3a107c9b44461d43e089190a461149a846117da565b50505050565b600a6020526000908152604090205460ff8116906101009004600160a060020a031682565b60008080610ba86001610ba0565b336000818152600760205260408120549091829160ff1615156114f557600080fd5b600160a060020a03841660009081526007602052604090205460ff16151561151c57600080fd5b600091505b6008548210156115d55783600160a060020a031660088381548110151561154457fe5b600091825260209091200154600160a060020a031614156115ca5760088054600019810190811061157157fe5b60009182526020909120015460088054600160a060020a03909216918490811061159757fe5b9060005260206000200160006101000a815481600160a060020a030219169083600160a060020a031602179055506115d5565b600190910190611521565b600160a060020a0384166000908152600760205260409020805460ff191690556008805460001901906116089082611b3e565b506116116117ce565b5050919050565b60055481565b600f805482908110610d4457fe5b60025481565b60096020908152600091825260409182902080548351601f6002600019610100600186161502019093169290920491820184900484028101840190945280845290918301828280156116c55780601f1061169a576101008083540402835291602001916116c5565b820191906000526020600020905b8154815290600101906020018083116116a857829003601f168201915b505050505081565b336000818152600c602052604081205490919060ff16156116ed57600080fd5b336000908152600c60209081526040909120805460ff191660019081178255865161171f939290910191870190611a79565b50336000908152600c60209081526040909120845161174692600290920191860190611a79565b50336000908152600c60209081526040909120865161176d92600390920191880190611a79565b50600d80546001818101835560008390527fd7b6990105719101dabeb77144f2a3385c8033acd3af97e9423a695e81ad1eb5909101805473ffffffffffffffffffffffffffffffffffffffff19163317905590546003559150509392505050565b60085460029004600155565b6000806117e6836118ce565b156118c9576000838152600a60209081526040808320805460ff198116600117909155600160a060020a036101009182900416808552600e9093529220805461ff001916909217909155915061183b8261195d565b50600090505b6006548110156118c9578260118281548110151561185b57fe5b906000526020600020015414156118c157601160016006540381548110151561188057fe5b906000526020600020015460118281548110151561189a57fe5b6000918252602090912001556011805460001901906118b99082611b3e565b506011546006555b600101611841565b505050565b600080805b60085460ff82161015611953576000848152600b602052604081206008805491929160ff851690811061190257fe5b6000918252602080832090910154600160a060020a0316835282019290925260400190205460ff1615611936576001820191505b60015460ff83161061194b5760019250611611565b6001016118d3565b5060009392505050565b600160a060020a0381166000908152600e602052604081205462010000900460ff1615156119ea5750600f80546001808201835560008390527f8d1108e10bcb7c27dddfc02ed9d693a074039d026cf4ea4240b40f7d581ac802909101805473ffffffffffffffffffffffffffffffffffffffff1916600160a060020a0385161790559054600455610d07565b6001600160a060020a0383166000908152600e602052604090205462010000900460ff161415610d075750601080546001808201835560008390527f1b6847dc741a1b0cd08d278845f9d819d87b734759afb55fe2de5cb82a9ae672909101805473ffffffffffffffffffffffffffffffffffffffff1916600160a060020a0385161790559054600555610d07565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f10611aba57805160ff1916838001178555611ae7565b82800160010185558215611ae7579182015b82811115611ae7578251825591602001919060010190611acc565b50611af3929150611b5e565b5090565b50805460018160011615610100020316600290046000825580601f10611b1d5750611b3b565b601f016020900490600052602060002090810190611b3b9190611b5e565b50565b8154818355818111156118c9576000838152602090206118c99181019083015b611b7891905b80821115611af35760008155600101611b64565b905600a165627a7a7230582041711ef78c48eaa5d2f57c71fa0b40eaa04e92fd98954feca288128f4f79a0bb0029",
            "storage": {
                "0x0000000000000000000000000000000000000000000000000000000000000000": "0x000000000000000000000000000000000000000000000000000000000000000f",
                "0x0000000000000000000000000000000000000000000000000000000000000001": "0x0000000000000000000000000000000000000000000000000000000000000000",
                "0x0000000000000000000000000000000000000000000000000000000000000002": "0x0000000000000000000000000000000000000000000000000000000000000001"
            }
        },
        "0xfffffffffffffffffffffffffffffffff0000002": {
            "balance": "1000000000000000000000000000",
            "code": "0x608060405234801561001057600080fd5b506001600081905550600180600073ca35b7d915458ef540ade6068dfe2f44e8fa733c73ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060006101000a81548160ff02191690831515021790555061039f806100936000396000f300608060405260043610610062576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff168063174847fb14610067578063451ec35d146100c2578063d38afa4f1461011d578063f3982e5e14610148575b600080fd5b34801561007357600080fd5b506100a8600480360381019080803573ffffffffffffffffffffffffffffffffffffffff1690602001909291905050506101a3565b604051808215151515815260200191505060405180910390f35b3480156100ce57600080fd5b50610103600480360381019080803573ffffffffffffffffffffffffffffffffffffffff1690602001909291905050506101c3565b604051808215151515815260200191505060405180910390f35b34801561012957600080fd5b50610132610299565b6040518082815260200191505060405180910390f35b34801561015457600080fd5b50610189600480360381019080803573ffffffffffffffffffffffffffffffffffffffff16906020019092919050505061029f565b604051808215151515815260200191505060405180910390f35b60016020528060005260406000206000915054906101000a900460ff1681565b60008160011515600160008373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060009054906101000a900460ff16151514151561022557600080fd5b6000600160008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060006101000a81548160ff0219169083151502179055506000808154809291906001900391905055506001915050919050565b60005481565b60008160001515600160008373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060009054906101000a900460ff16151514151561030157600080fd5b60018060008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060006101000a81548160ff021916908315150217905550600080815480929190600101919050555060019150509190505600a165627a7a723058200b1ba6fa8e67e46ff3aa909ddfc55f2c9fad320c6425aec0c7a61baad3fe33490029"
        }
    },
    "coinbase":"0x0000000000000000000000000000000000000000",
    "timestamp": "0x00",
    "parentHash":"0x0000000000000000000000000000000000000000000000000000000000000000",
    "extraData": "0x000000",

    "config":{
        "chainId":15
    },

    "gasLimit":"0xffffffff"
}
```

The above fields should be fine for most purposes, although we'd recommend changing the `nonce` to
some random value so you prevent unknown remote nodes from being able to connect to you. If you'd
like to pre-fund some accounts for easier testing, you can populate the `alloc` field with account
configs:

```json
"alloc": {
  "0x0000000000000000000000000000000000000001": {"balance": "111111111"},
  "0x0000000000000000000000000000000000000002": {"balance": "222222222"}
}
```

With the genesis state defined in the above JSON file, you'll need to initialize **every** usechain node
with it prior to starting it up to ensure all blockchain parameters are correctly set:

```
$ used init path/to/genesis.json
```

#### Creating the rendezvous point

With all nodes that you want to run initialized to the desired genesis state, you'll need to start a
bootstrap node that others can use to find each other in your network and/or over the internet. The
clean way is to configure and run a dedicated bootnode:

```
$ bootnode --genkey=boot.key
$ bootnode --nodekey=boot.key
```

With the bootnode online, it will display an [`enode` URL](https://github.com/usechain/wiki/wiki/enode-url-format)
that other nodes can use to connect to it and exchange peer information. Make sure to replace the
displayed IP address information (most probably `[::]`) with your externally accessible IP to get the
actual `enode` URL.

*Note: You could also use a full fledged usechain node as a bootnode, but it's the less recommended way.*

#### Starting up your member nodes

With the bootnode operational and externally reachable (you can try `telnet <ip> <port>` or `nc -v <ip> <port>` to ensure
it's indeed reachable), start every subsequent usechain node pointed to the bootnode for peer discovery
via the `--bootnodes` flag. It will probably also be desirable to keep the data directory of your
private network separated, so do also specify a custom `--datadir` flag.

```
$ used --datadir=path/to/custom/data/folder --bootnodes=<bootnode-enode-url-from-above>
```

*Note: Since your network will be completely cut off from the main and test networks, you'll also
need to configure a miner to process transactions and create new blocks for you.*

#### Running a private miner

Mining on the public usechain network is a complex task as it's only feasible using GPUs, requiring
an OpenCL or CUDA enabled `ethminer` instance. For information on such a setup, please consult the
[EtherMining subreddit](https://www.reddit.com/r/EtherMining/) and the [Genoil miner](https://github.com/Genoil/cpp-ethereum)
repository.

In a private network setting however, a single CPU miner instance is more than enough for practical
purposes as it can produce a stable stream of blocks at the correct intervals without needing heavy
resources (consider running on a single thread, no need for multiple ones either). To start a usechain
instance for mining, run it with all your usual flags, extended by:

```
$ used <usual-flags> --mine --minerthreads=1 --etherbase=0x0000000000000000000000000000000000000000
```

Which will start mining blocks and transactions on a single CPU thread, crediting all proceedings to
the account specified by `--etherbase`. You can further tune the mining by changing the default gas
limit blocks converge to (`--targetgaslimit`) and the price transactions are accepted at (`--gasprice`).

## Contribution

Thank you for considering to help out with the source code! We welcome contributions from
anyone on the internet, and are grateful for even the smallest of fixes!

If you'd like to contribute to go-usechain, please fork, fix, commit and send a pull request
for the maintainers to review and merge into the main code base. If you wish to submit more
complex changes though, please check up with the core devs first on [our gitter channel](https://github.com/usechain)
to ensure those changes are in line with the general philosophy of the project and/or get some
early feedback which can make both your efforts much lighter as well as our review and merge
procedures quick and simple.

Please make sure your contributions adhere to our coding guidelines:

 * Code must adhere to the official Go [formatting](https://golang.org/doc/effective_go.html#formatting) guidelines (i.e. uses [gofmt](https://golang.org/cmd/gofmt/)).
 * Code must be documented adhering to the official Go [commentary](https://golang.org/doc/effective_go.html#commentary) guidelines.
 * Pull requests need to be based on and opened against the `master` branch.
 * Commit messages should be prefixed with the package(s) they modify.
   * E.g. "eth, rpc: make trace configs optional"

Please see the [Developers' Guide](https://github.com/usechain/go-usechain/wiki/Developers'-Guide)
for more details on configuring your environment, managing project dependencies and testing procedures.

## License

The go-usechain library (i.e. all code outside of the `cmd` directory) is licensed under the
[GNU Lesser General Public License v3.0](https://www.gnu.org/licenses/lgpl-3.0.en.html), also
included in our repository in the `COPYING.LESSER` file.

The go-usechain binaries (i.e. all code inside of the `cmd` directory) is licensed under the
[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html), also included
in our repository in the `COPYING` file.
contributions adhere to our coding guidelines:

 * Code must adhere to the official Go [formatting](https://golang.org/doc/effective_go.html#formatting) guidelines (i.e. uses [gofmt](https://golang.org/cmd/gofmt/)).
 * Code must be documented adhering to the official Go [commentary](https://golang.org/doc/effective_go.html#commentary) guidelines.
 * Pull requests need to be based on and opened against the `master` branch.
 * Commit messages should be prefixed with the package(s) they modify.
   * E.g. "eth, rpc: make trace configs optional"

Please see the [Developers' Guide](https://github.com/usechain/go-usechain/wiki/Developers'-Guide)
for more details on configuring your environment, managing project dependencies and testing procedures.