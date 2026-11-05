# dotenv

Read `.env` files in Stenopos: one `KEY=VALUE` per line, into an ordered set
of variables. The first package published to the Stenopos registry.

```bash
tagma add stenopos/dotenv
```

`tagma add` grants it the one capability its code uses, `File` (for `load`):

```toml
[dependencies]
dotenv = { package = "stenopos/dotenv", version = "1.0.0", allow = ["File"] }
```

```stenopos
import dotenv.{Dotenv, load}

@cap(File)
func main() -> void {
    Dotenv env = load(".env")
    for (p in env.problems) {
        eprintln(".env: " + p)
    }
    println("port " + env.get_or("PORT", "8080"))
}
```

## The format

```bash
# a comment
export PORT=8080            # `export ` is allowed and ignored
NAME = Stenopos             # spaces around `=` and the value are trimmed
GREETING="Hello\nworld"     # double quotes: \n \t \r \" \\ escapes
PATTERN='a\nb'              # single quotes: the text exactly as written
EMPTY=
URL=http://x/#top # note    # ` #` starts a comment in an unquoted value
```

- Values are taken literally: `$VAR` and `${VAR}` are not expanded.
- A key is letters, digits and `_`, not starting with a digit.
- When a key appears twice, the last value wins and the key keeps its first
  place.
- A line that can't be read is skipped and described in `problems`
  (`line 3: expected KEY=VALUE`); the rest of the file is still read.
- A missing or empty file has no variables.

## API

| | |
|---|---|
| `parse(str text) -> Dotenv` | The variables in a file's text (pure) |
| `load(str path) -> Dotenv` | The variables in the file at `path` (`@cap(File)`) |
| `Dotenv.get(str key) -> str?` | A value, or null when the file doesn't set it |
| `Dotenv.get_or(str key, str fallback) -> str` | A value, or `fallback` |
| `Dotenv.has(str key) -> bool` | Whether the file sets `key` |
| `Dotenv.len() -> int32` | How many variables it sets |
| `Dotenv.keys`, `.values` | The variables, in order |
| `Dotenv.problems` | One message per line that couldn't be read |
| `valid_key(str key) -> bool` | Whether `key` can be a key |

## Releasing

Raise `version` in `Tagma.toml`, commit, and tag the commit `v<version>`.
CI tests the tagged commit, finds its capabilities, and publishes the
version to the registry, signed by the Stenopos CI publisher key.

This repository is developed on the Stenopos Gitea server; the GitHub copy is
published automatically after every build and test passes.

*Ἀσφάλεια περὶ πλείστου: security above all.*
