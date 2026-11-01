# Sat Rescue

A small, **non-custodial** web tool to recover BTC that ordinals wallets lock as
*"unavailable balance"* — the plain bitcoin trapped inside UTXOs that also carry an
inscription or a rune.

**▶ Live tool: https://satrescue.app/**

> **Don't trust, verify.** This repository is the full source of the tool. Read
> [`index.html`](index.html) before you connect any wallet. Your keys never leave your
> machine — the page only ever receives a signature your own wallet produces.

## The problem

Ordinals-aware wallets (UniSat, Xverse, …) deliberately **lock** any UTXO that contains
an inscription or a rune, so you don't accidentally spend a collectible as fees. The side
effect: the plain BTC value sitting in those outputs — often hundreds of tiny dust UTXOs
left over from drop farming, minting and trading — shows up as **"unavailable balance"**,
and the wallet's normal Send button won't touch it.

The coins are yours and clearly visible, but the wallet UI refuses to move them.

## What this does

1. You connect your wallet — read-only; it reads your address and public key.
2. It scans your UTXOs, separates inscription/rune outputs from plain dust, and builds the
   sweep transaction (a PSBT).
3. Two modes:
   - **Consolidate** the plain BTC inside your own wallet (inscriptions/runes are kept
     segregated in a single output, so nothing is burned).
   - **Sweep to an external address** you choose (e.g. an exchange deposit address).
4. **Your wallet shows you the transaction and you sign it.** The signed transaction is
   broadcast. That's all.

Inscribed inputs are ordered first and sized so their sats land only in your own/clean
output — never in the miner fee or the service output.

## Safety

- **Non-custodial.** The site never sees, stores or transmits your seed or private key.
- **Nothing is auto-sent.** You review every output and amount in your wallet before signing.
- The destination can be any address you control.
- Static page, no backend for your keys — serve it yourself if you prefer (see below).

## Fee

A service fee of **17%** of the recovered amount is included as one output in the same
transaction. You see it in your wallet before signing, and it only applies when the sweep
actually nets coins above the dust threshold. The miner fee is the current network rate.

If you are comfortable building PSBTs yourself, you don't need this tool — it just
automates that assembly in a couple of clicks.

## Run it yourself

It's a static page. Either open `index.html` in a browser, or serve the folder:

```bash
npx serve .
```

`lib.js` is a bundled, unmodified build of
[`@scure/btc-signer`](https://github.com/paulmillr/scure-btc-signer) and
[`@scure/base`](https://github.com/paulmillr/scure-base) (audited Bitcoin libraries),
so the page needs no external scripts at runtime.

## Disclaimer

Provided as-is, without warranty. Bitcoin transactions are irreversible — always review
the inputs, outputs and amounts your wallet shows you before you sign. You are responsible
for your own funds.
