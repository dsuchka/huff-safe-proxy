# SafeProxy contract

A small, simple, and low-cost (gas-optimized) proxy contract
with a special hardcoded (immutable) «Safe-User» address who's
allowed to run anything operations (through special `0x5AFECA11`
method to perform `delegatecall` on the contract).

It aims to protect your Gems (a Smart-Contract project) from losing access
by accidental write illegal data to the owner slot or somehow else.

Advantages:
- No way to erase the «Safe-User»

Disadvantages:
- The «Safe-User» cannot be changed, don't lose control over it

## Contract constructor

Solidity format: `(address,address)`

There are 2 args (32-bytes word each):
- 1st: the «Safe-User» (it will become immutable!)
- 2nd: the implementation address (can be changed later)

## Contract methods:

1. `contract_name()` returns `(string)`
    * Returns string: `SafeProxy`

1. `contract_version()` returns `(string)`
    * Returns string: `1.0.0`

1. Raw selector `0x5AFECA11`: \
    Raw payload (following the selector): `<target(20)>` `<signature(65)>` `<target_payload(+)>` \
    _NOTE_: all data are packet
    - `target` (20 bytes) — a contract address to run `delegatecall` to
    - `signature` (65 bytes) — a signature to the call (see below)
    - `target_payload` (any size) — data to pass to the `delegatecall`

    Signature hash: `keccak(<target_payload...> <nonce> <caller> <self> <target>)` \
    _NOTE_: only the `<target_payload...>` is packet, `nonce`, `caller (msg.sender)`,
    `self (proxy address)`, and `target` are 32-bytes each with leading zero paddings.

    Signature must belong to the «Safe-User».

    The `nonce` is incremented automatically after each non-reverted call.

1. All other calls will be passed to the implementation. \
    _NOTE_: there is no `implementation()` method, but an implementation can implement it
    if necessary.

## Contract slots:
- \[0\] (Zero slot): The `caller` (msg.sender) will be written to the zero slot by constructor,
    an implementation can relay on the zero slot as owner slot for the contract if you want.
- \[0xa09c2e16848101e8\] (`keccak256('SafeProxy::implementation')[:8]`): `IMPLEMENTATION_SLOT`
- \[0x132d3bbee0e78cfa\] (`keccak256('SafeProxy::nonce')[:8]`): `NONCE_SLOT`

_NOTE_: to change any data at any slot you have to run an implementation method (if it supports it),
    otherwise it possible only via the «Safe-Call» (0x5AFECA11) method using additional contract with
    ability to write any data at specified slot.


## Contract errors:
- `WrongArgument()`: when args are wrong (contructor expects 2 32-bytes words, 0x5AFECA11 minimum (20+65) bytes of payload (except 4 bytes of the selector))
- `WrongSignature()`: when signature is illegal (wrong `r`, `s`, or `v` components, `ecrecover` failed)
- `Forbidden()`: when signature is incorrect (belongs to not the «Safe-User»)


## License

This project is licensed under the BUSL-1.1 License.

## Donations

If you find this project useful, please consider supporting it. \
I'd be incredibly grateful for any support you’d like to give!

**Wallets:**
- TRX: TDsuchka3yaV2TBRVa5uZdMhCNKeTsT6zm
- BTC: 1DsuchKa1y2PrhcaGoBdSZsJM2ikiyNC4s
- LTC: LdsuchkaBL1ciupWzg9feH7LatiHvYZbmp
- ETH: 0x7777777773fF72D55b4dd7fBD34f3149953c96D4

