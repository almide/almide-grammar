# almide-grammar

Single source of truth for Almide syntax definitions — keywords, operators, precedence, and TextMate scopes.

Written in [Almide](https://github.com/almide/almide). All consumers import this module to stay in sync.

## Usage

### As an Almide dependency

Add to your `almide.toml`:

```toml
[dependencies]
almide-grammar = { git = "https://github.com/almide/almide-grammar", tag = "v0.1.0" }
```

Then import:

```almide
import almide_grammar

for group in almide_grammar.keyword_groups() {
  println(group.category ++ ": " ++ string.join(group.words, " "))
}
```

### As a CLI

```bash
almide run almide-grammar <target>
```

| Target | Output |
|--------|--------|
| `tree-sitter` | Keyword rules + precedence for grammar.js |
| `textmate` | JSON patterns for tmLanguage |
| `rust` | Keyword map + `ALL_KEYWORDS` for the compiler lexer |
| `info` | Human-readable summary of all keywords and precedence |

## API

| Function | Return type | Description |
|----------|-------------|-------------|
| `keyword_groups()` | `List[KeywordGroup]` | 6 groups: control, declaration, modifier, value, flow, other |
| `keyword_aliases()` | `List[(String, String)]` | Case aliases: `Ok`→`ok`, `Err`→`err`, `Some`→`some`, `None`→`none` |
| `precedence_table()` | `List[PrecLevel]` | 8 levels from pipe (1) to unary (8) |
| `all_keywords()` | `List[String]` | All 41 keywords, sorted |

## Structure

```
almide-grammar/
  almide.toml         package: almide_grammar v0.1.0
  tokens.toml         keyword/operator definitions (for compiler build.rs)
  precedence.toml     operator precedence table (for compiler build.rs)
  src/
    mod.almd          library entry point — all data definitions
    main.almd         CLI — imports mod.almd via `import self as grammar`
```

## Consumers

| Project | How it uses almide-grammar |
|---------|---------------------------|
| [almide](https://github.com/almide/almide) (compiler) | `build.rs` reads `tokens.toml` / `precedence.toml` → generates `src/generated/token_table.rs` |
| [almide-editors](https://github.com/almide/almide-editors) | Almide dependency → `import almide_grammar` in TextMate generator |

## License

MIT
