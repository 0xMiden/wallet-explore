# wallet-explore

The Explore catalog the Miden wallet shows, one document per network, read at runtime from
`https://raw.githubusercontent.com/0xMiden/wallet-explore/main/<network>.json`, with each app's icon
from `icons/` here. Whatever is on `main` is what every wallet on that network shows; there is no
signature.

## What being listed means

Only that the app appears in Explore. It grants nothing: the wallet shows no badge or other sign of
trust for a listed app, and asks the same connect and signing questions of it as of any site.

## Fields

| Field | Meaning |
|---|---|
| `network` | The network this file is for; must equal the file name (`testnet.json` holds `"testnet"`). |
| `version` | Positive integer. A wallet never goes back to a lower version than one it has accepted. |
| `items[].id` | `a-z`, `0-9` and `-`, unique among the items. |
| `items[].name` | Text (below), at most 40 characters in each language. |
| `items[].tagline` | Text, at most 120 characters in each language. |
| `items[].url` | The page the app opens: `https:`, no user name or password, no IP address, `localhost` or punycode (`xn--`) host. A query is fine. Kept exactly as written. |
| `items[].category` | `tools`, `defi`, `games`, `nft` or `learn`: the wallet's category chips. |
| `items[].icon` | Optional. `icons/<name>.png`, a file in this repository (below). Without one the wallet draws the app's initial. |
| `items[].brandColor` | Optional. `#RRGGBB`, behind the icon on a featured card. |
| `items[].isExchange` | `true` for a swap or exchange; the wallet hides those wherever it ships without swap. |
| `sections[].id` | `a-z`, `0-9` and `-`, unique among the sections. `recents` and `search-results` are taken by the sections the wallet adds. |
| `sections[].kind` | `featured` (large cards) or `list` (rows). |
| `sections[].title` | Text. |
| `sections[].itemIds` | Item ids in display order, each once. |

Text is an object of literal strings by wallet locale code: `en` is required; `de`, `en_GB`, `es`,
`fr`, `ja`, `ko`, `pl`, `pt`, `ru`, `tr`, `uk`, `zh_CN` and `zh_TW` are optional. A wallet shows its
own language, else that language's base (`pt` for Brazilian Portuguese), else `en`. A key that is
not a wallet locale is ignored, so a language can be added before every wallet ships it.

The wallet adds Recents after the sections and draws search results itself; a document can neither
move nor remove them. An item in a category, or a section of a kind, that a wallet does not know is
left out by that wallet, so a new one can be added before every wallet understands it; every other
unknown field is ignored. Any other mistake makes a wallet ignore the whole document and keep the
catalog it has.

An icon is a PNG of at most 32 KiB, square and at most 256x256, committed as a regular file with no
symbolic link in its path. The whole document stays within 32 KiB, the most a wallet reads.

## Publishing

1. Edit the document and bump `version`. Going back to an older catalog is a new, higher version too.
2. Open a pull request. `validate` checks the document against the wallet's own rules, the version
   bump against `main`, the document size and every icon.
3. Merge (squash). A wallet picks the change up at its next hourly check, or on its return to the
   foreground once its copy is more than 15 minutes old, plus up to about 5 minutes of raw CDN cache.

A wallet release bundles a copy of each document for its first start, and its release job reports a
bundled copy that no longer matches `main`.

Run the checks locally with Node 22: `node --test scripts/validate.test.mjs` and
`node scripts/validate.mjs` (`BASE_REF=origin/main node scripts/validate.mjs` adds the version rule).
