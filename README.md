# PancakeSwap Token Lists

## Install
use [Bun](https://bun.sh/)
```sh
curl -fsSL https://bun.sh/install | bash
```

```sh
bun install
```

This repo contains main PancakeSwap token list and tools to validate it.

## How do I list my token?

There are 2 realistic ways to get your token into a PancakeSwap-compatible list:

1. Add your own external token list URL to this repository.
2. Add your token to a list in your own fork and publish that list yourself.

If you are trying to get listed on the official PancakeSwap default or extended lists, note that normal pull requests for that are generally **not accepted** unless you were specifically asked by the PancakeSwap team.

## How to add external lists

URLs to external lists are stored in `lists/token-lists.json`.

If you want your list to be available in the list UI, submit a PR updating `lists/token-lists.json`.

Example:

```json
{
  "https://example.com/my-token-list.json": {
    "name": "My Token List",
    "homepage": "https://example.com"
  }
}
```

Your hosted list should be a valid token list JSON file and should be publicly accessible over HTTPS.

## How to add new lists within this repository

- Add an array of tokens under `src/tokens`
- Add `checksum newlistname`, `generate newlistname`, `makelist newlistname` command to `package.json` analogous to PancakeSwap default and extended list scripts.
- Modify `checksum.ts`, `buildList.ts`, `ci-check.ts`, and `default.test.ts` to handle new list

## How to add new tokens to PancakeSwap (extended) token list

Note - this is not something we expect pull requests for.
Unless you've been specifically asked by someone from the PCS team, please do not submit PRs to be listed on the default PCS list. You can still trade your token on PancakeSwap by pasting its address into the token field.

- Add new tokens to `src/tokens/pancakeswap-extended.json` file
- Run `bun makelist pancakeswap-extended`
  - By default new list will have patch version number bumped by 1 (e.g. `2.0.1` -> `2.0.2`).
  - If you want to bump minor version add `minor` after makelist command `bun makelist pancakeswap-extended minor`
  - If you want to bump major version add `major` after makelist command `bun makelist pancakeswap-extended major`
- If tests pass - new token list will be created under `lists` directory

### Minimum token data you will need

For any token entry, make sure you have:

- `chainId`
- `address`
- `name`
- `symbol`
- `decimals`
- a valid logo asset if the target list expects one

If you are creating your own list in this repo, add the token to the relevant file under `src/tokens`, then run:

```sh
bun makelist <list-name>
```

That will generate the final JSON list under `lists/`.

For a list to be considered valid, it needs to satisfy the following criteria:

- It passes [token list schema](https://github.com/Uniswap/token-lists/blob/master/src/tokenlist.schema.json) validation (schema is [slightly modified](src/schema.ts))
- There are no duplicate addresses, symbols or token names in the list
- All addresses are valid and checksummed (`bun checksum listName` automatically converts addresses to checksummed versions, it is also part of `bun makelist listName`)

## How to update Top100 Token list

Note - this is not something we expect pull requests for.

```shell script
# Fetch the Top100 Tokens on PancakeSwap v2, and update list.
$ bun fetch pancakeswap-top-100

# Build token list (pancakeswap-top-100.json)
$ bun makelist pancakeswap-top-100
```

## Deploying

Token lists will be auto-deployed via Cloudflare Pages when PR is merged to master. Be sure to build the list with `bun makelist list-name` before submitting/merging the PR since it doesn't make much sense building lists within Pages (because most errors are related to wrong token information and should be fixed prior to landing into master)

Pages simply takes the json files under `lists` directory and hosts them on `tokens.pancakeswap.finance/list-name.json`
