Seth [![GitHub (pre-)release](https://img.shields.io/github/release/dapphub/seth/all.svg)](https://github.com/dapphub/seth/releases)
========================================================================

Seth is an Ethereum client tool—like a "MetaMask for the command
line"—maintained by [the DappHub collective].

:older_woman: If you're a **command-line hacker**, Seth will make you
go *"It's a Unix system—I know this!"*

:fax: If you're doing **blockchain automation**, Seth is an excellent
base for deploy scripts, integration tests, and bots.

:money_with_wings: Or if you love the idea of **open source finance**,
Seth is a sci-fi future where you can securely manage funds and
contracts from the comfort of your command line.

**New:** Seth supports signing transactions with [Ledger Nano S]
hardware wallets.

> "One indicator I look for in a healthy open source project is how
  many useful tools come out of its team as a side effect of their
  efforts."
  —[`@danfinlay`](https://twitter.com/danfinlay/status/942909341044162560])

> "Looks like a great set of CLI tools, very devopsy." —Andreas Antonopolous

> "The Unix approach you've taken is perfect." —`immutability`

Seth is also the basis for other tools—such as the [MakerDAO
CDP utility], which lets you interact with the Dai stablecoin system
from your shell.

Contents
------------------------------------------------------------------------

  1. [Installing](#installing)
    1.1. [Upgrading](#upgrading)
  2. [Configuration](#configuration)
    2.1. [Example `.sethrc` file](#example-sethrc-file)
    2.2. [Connecting to the blockchain](#connecting-to-the-blockchain)
    2.3. [Key management and signing](#key-management-and-signing)
    2.4. [Your address](#your-address)
  3. [Basic usage: a tutorial](#basic-usage-a-tutorial)
    3.1. [Ether transactions](#ether-transactions)

Installing
------------------------------------------------------------------------

Seth is distributed via [the Nix package manager], enabling
cryptographically precise dependency tracking.  First, install Nix
itself:

    $ curl https://nixos.org/nix/install | sh

Then add DappHub's distribution channel and install `seth`:

    $ nix-channel --add https://nix.dapphub.com/pkgs/dapphub
    $ nix-channel --update
    $ nix-env -iA dapphub.seth

See [dapp.tools](https://dapp.tools) for more software available
through our channel.

### Upgrading

To upgrade Seth to the latest release, update the channel and then
reinstall:

    $ nix-channel --update
    $ nix-env -iA dapphub.seth


Configuration
------------------------------------------------------------------------

Seth has options that can be specified via command-line flags
or environment variables.

For convenience, Seth looks for `~/.sethrc` and loads it as a shell
script (Bash 4 syntax).  This is a convenient place to set default
options by exporting environment variables.

### Example `.sethrc` file

The `~/.sethrc` file is just a regular Bash script that is
automatically loaded by Seth.  Here is an example:

    # Use Infura's mainnet node for all RPC calls
    export SETH_CHAIN=ethlive

    # Set an address as the default sender
    export ETH_FROM=0xd08f67044c53d723686e002c5b880f73674e164c

    # Look for my key files in a custom directory
    export ETH_KEYSTORE=~/secrets/ethereum

Note that flags given to the `seth` command will override
these settings.

### Connecting to the blockchain

By default, Seth assumes a local RPC node on the standard port.

You can specify another RPC URL using the variable `ETH_RPC_URL`
or the flag `--rpc-url`.

Alternatively, you can use a default remote node (operated by
[Infura]) using the variable `SETH_CHAIN` or the flag `--chain`.
Allowed values: `ethlive` (aka `mainnet`), `ropsten`, `kovan`, and
`rinkeby`.

### Key management and signing

By default, Seth does not use the RPC node for key management or
signing transactions.  Instead, it uses keys stored on your machine,
as well as your Ledger Nano S hardware wallet (if present).  **Thus,
you do not need to "unlock" your account in Geth or Parity.**

Seth looks for keys in the standard directories of Geth and Parity.
To configure a custom location for your key files, use the
`ETH_KEYSTORE` variable or the `--keystore` flag.

If your key is protected with a password, Seth will prompt you each
time you make a transaction.  If you are confident in your computer
security, and you want to (say) run a bot script, you can set the
`ETH_PASSWORD` variable (flag: `--password`) to point to a file
containing your password.

If you do want to use the RPC node for key management and signing, set
the `ETH_RPC_ACCOUNTS` variable or use the `--rpc-accounts` flag.
This probably means you need to use Geth's or Parity's account
management tools to "unlock" your account.

### Your address

When making transactions or doing read-only calls, Seth takes the
sending address from the `ETH_FROM` variable or the `--from` flag.

Note for Ledger Nano S hardware wallet users: Seth currently only
looks for the first four addresses derived from your seed phrase.
If the sending address is not one of those, Seth will not be able
to sign transactions.


Basic usage: a tutorial
------------------------------------------------------------------------

This section assumes that you have something like the example
`.sethrc` file specifying how to connect to the blockchain
and a default sender address.

### Ether transactions

Here is how you might send one wei—the smallest possible amount of
ether—to the [Ethereum Foundation's donation address]:

    $ [seth send](#seth-send) --value 1 0xfB6916095ca1df60bB79Ce92cE3Ea74c37c5d359
    seth-send: warning: `ETH_GAS' not set; using default gas amount
    Ethereum account passphrase (not echoed):
    seth-send: Published transaction with 0 bytes of calldata.
    seth-send: 0xe428d4bb148ded426777ae892578507e4f394f608ad9d3a9d0229e8348ba72e3
    seth-send: Waiting for transaction receipt...
    seth-send: Transaction included in block 4908738.

Now you can check the balance of the donation fund:

    $ [seth balance](#seth-balance) 0xfB6916095ca1df60bB79Ce92cE3Ea74c37c5d359
    2963.72865500027557173E+18

You can also check the ether balances of your own accounts:

    $ [seth ls](#seth-ls)
    0xCC41D9831E4857B4F16914A356306fBeA734183A    0.24E+18
    0xD9ceccea2BEE9a367d78658aBbB2Fe979b3877Ef    0.03409E+18

Generally, you don't transact in terms of wei amounts, but in
fractional amounts of ether.  You can convert an ether amount into a
wei amount using [`seth --to-wei`](#seth-to-wei).  Here, we send 1.5
ETH:

    $ fund=0xfB6916095ca1df60bB79Ce92cE3Ea74c37c5d359
    $ seth send --value $(seth --to-wei 1.5 eth) $fund

(The `$(...)` shell syntax for ["command substitution"] is useful with
Seth.  It allows the output of one command to become a parameter
to another.)



[the DappHub collective]: https://dapphub.com

[Ledger Nano S]: https://www.ledgerwallet.com/products/ledger-nano-s

[Infura]: https://infura.io

["command substitution"]: https://www.gnu.org/software/bash/manual/html_node/Command-Substitution.html

[Ethereum Foundation's donation address]: https://www.ethereum.org/donate

[the Nix package manager]: https://nixos.org/nix

[MakerDAO CDP utility]: https://github.com/makerdao/dai-cli
