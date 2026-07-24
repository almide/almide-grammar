# almide-grammar

Single source of truth for Almide syntax definitions — keywords, operators, precedence, and TextMate scopes.

Written in [Almide](https://github.com/almide/almide). All consumers import this module to stay in sync.

The data is a descriptive mirror of the compiler (`crates/almide-syntax`); the
executable truth for precedence is the compiler's
`parser/test_expr_precedence.rs`.

## Usage

### As an Almide dependency

Add to your `almide.toml`:

```toml
[dependencies]
almide-grammar = { git = "https://github.com/almide/almide-grammar" }
```

Then import:

```almide
import almide_grammar

for group in almide_grammar.keyword_groups() {
  println(group.category + ": " + group.words.join(" "))
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
| `keyword_groups()` | `List[KeywordGroup]` | 5 groups: control, declaration, modifier, value, flow |
| `keyword_aliases()` | `List[(String, String)]` | Case aliases: `Ok`→`ok`, `Err`→`err`, `Some`→`some`, `None`→`none` |
| `precedence_table()` | `List[PrecLevel]` | 11 levels from `or` (1) to postfix (11) |
| `all_keywords()` | `List[String]` | All 35 keywords, sorted |

Two precedence subtleties a flat table cannot express (see `precedence.toml`):
`|>` is asymmetric (its RHS is a single postfix/compose chain — only `>>`
nests inside it), and `??` takes only a unary expression as its fallback.

## Structure

```
almide-grammar/
  almide.toml         package: almide_grammar v0.1.0
  tokens.toml         keyword/operator definitions
  precedence.toml     operator precedence table
  src/
    mod.almd          library entry point — all data definitions
    main.almd         CLI — imports mod.almd via `import self as grammar`
```

## Consumers

| Project | How it uses almide-grammar |
|---------|---------------------------|
| [almide](https://github.com/almide/almide) (compiler) | `grammar/` git submodule — descriptive reference; parity is enforced by the compiler's own lexer/parser tests |
| [tree-sitter-almide](https://github.com/almide/tree-sitter-almide) | Almide dependency → `import almide_grammar` in the grammar.js generator |
| [vscode-almide](https://github.com/almide/vscode-almide) | Almide dependency → `import almide_grammar` in the TextMate generator |

## License

MIT
