# Vrata

A Hiccup-style web framework for [Irij](https://github.com/laniakea-etc/irij).

Vrata provides structured HTML generation, CSS helpers, and page templates — replacing manual string concatenation with composable element trees.

## Install

Add to your `irij.toml`:

```toml
[seeds]
vrata = { git = "https://github.com/aldebogdanov/vrata.git", tag = "v0.1.2" }
```

## Usage

```irij
use vrata.html :open
use vrata.css :open
use vrata.page :open
use std.serve :open

fn my-css (-> stylesheet #[
  (rule "body" "font-family:sans-serif;margin:0")
  (rule ".container" "max-width:960px;margin:0 auto;padding:2rem")
  (rule "h1" "color:#333")
])

fn index-page req =>
  html ~ page "Home" (my-css ()) #[] #[
    (div (cls "container") #[
      (h1 {} #["Hello from Vrata"])
      (p {} #["Hiccup-style HTML for Irij."])
      (a {href= "/about"} #["About"])
    ])
  ]

fn about-page req =>
  html ~ page "About" (my-css ()) #[] #[
    (div (cls "container") #[
      (h1 {} #["About"])
      (p {} #["Built with Irij + Vrata."])
    ])
  ]

routes := #[
  (route "GET" "/" index-page)
  (route "GET" "/about" about-page)
]

with default-serve
  serve 8080 (router routes)
```

## Modules

### `vrata.html`

Core HTML rendering. Elements are maps `{tag= attrs= children=}`.

| Function | Signature | Description |
|----------|-----------|-------------|
| `el` | `Str Map Vec -> Map` | Create element: `el "div" {class= "x"} #[...]` |
| `render` | `a -> Str` | Render element tree to HTML string |
| `raw` | `Str -> Map` | Raw HTML (no escaping) |
| `cls` | `Str -> Map` | Shorthand for `{class= c}` |
| `html-escape` | `Str -> Str` | Escape `& < > "` |

**Tag functions** — each takes `(attrs children)`:

`div`, `span`, `section`, `article`, `aside`, `header`, `footer`, `nav`, `main`,
`p`, `a`, `em`, `strong`, `small`, `code`, `pre`, `blockquote`,
`h1`–`h6`, `ul`, `ol`, `li`,
`table`, `thead`, `tbody`, `tfoot`, `tr`, `th`, `td`,
`form`, `button`, `label`, `select`, `option`, `textarea`,
`style`, `script`, `title`

**Void elements** — take only `(attrs)`:

`img`, `br`, `hr`, `input`, `meta-tag`, `link-tag`

### `vrata.css`

| Function | Signature | Description |
|----------|-----------|-------------|
| `rule` | `Str Str -> Str` | CSS rule: `rule ".card" "padding:1rem"` |
| `stylesheet` | `Vec -> Str` | Join rules into stylesheet string |
| `media` | `Str Vec -> Str` | Media query wrapper |

### `vrata.page`

| Function | Signature | Description |
|----------|-----------|-------------|
| `page` | `Str Str Vec Vec -> Str` | Full HTML5 document: `page title css head-extras body-children` |

## Key rules

Inside `#[...]` vector literals, **wrap function calls in parens**:

```irij
;; Correct
div {} #[(h1 {} #["Title"]) (p {} #["Text"])]

;; Wrong — would flatten into separate elements
div {} #[h1 {} #["Title"]]
```

Strings and literals don't need parens:

```irij
p {} #["Hello " (em {} #["world"]) "!"]
```

## Name

*Vrata* (врата) — Old Slavic for "gate" or "portal".

## License

MIT
