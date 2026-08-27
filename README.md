<h1 align="center">tree.mq</h1>

A tree-rendering utility module for [mq](https://github.com/harehare/mq). Renders nested data (dicts, arrays, scalars) or flat path lists as ASCII tree art, similar to the Unix `tree` command.

## Features

- Render any tree node as ASCII tree art (`├──`, `└──`, `│`)
- Convert arbitrary mq values (dicts, arrays, scalars) into a tree
- Build a tree from a flat list of path strings (e.g. file paths, `ghq list` output)
- Minimal node representation (`{"label": ..., "children": [...]}`) so trees can also be built by hand

## Installation

Copy `tree.mq` to your mq module directory, or reference it with `-L`.

```sh
cp tree.mq ~/.local/mq/config/
```

### HTTP Import (no local installation needed)

HTTP imports are disabled by default; pass `--allow-http-import` to import directly from GitHub without any local setup:

```sh
mq --allow-http-import -I raw 'import "github.com/harehare/tree.mq" | tree::tree_render_paths(split(., "\n"))' files.txt
```

Pin to a specific release with `@vX.Y.Z`:

```sh
mq --allow-http-import -I raw 'import "github.com/harehare/tree.mq@v0.1.0" | ...'
```

## Usage

```sh
mq -L /path/to/modules -I raw \
  'import "tree" | tree::tree_render_paths(split(., "\n"))' files.txt
```

## API

### Node Constructors

| Function | Description |
|---|---|
| `tree_leaf(label)` | Creates a leaf node with no children |
| `tree_branch(label, children)` | Creates a branch node from a label and an array of child nodes |

### Conversion

| Function | Description |
|---|---|
| `tree_from_value(value, label="root")` | Recursively converts a dict, array, or scalar into a tree node |
| `tree_from_paths(paths, sep="/")` | Builds a tree node from an array of path strings, rooted at `.`. `sep` is matched as a regex (escape metacharacters, e.g. `"\\."`) |

### Rendering

| Function | Description |
|---|---|
| `tree_render(node)` | Renders a tree node as ASCII tree art |

### Shortcuts

| Function | Description |
|---|---|
| `tree_render_value(value, label="root")` | `tree_render(tree_from_value(value, label))` in one call |
| `tree_render_paths(paths, sep="/")` | `tree_render(tree_from_paths(paths, sep))` in one call |

## Example

### Rendering arbitrary data

```sh
mq -L . -I raw 'import "tree" | tree::tree_render_value({"name": "Alice", "pets": ["cat", "dog"]})'
```

```
root
├── name: Alice
└── pets
    ├── [0]: cat
    └── [1]: dog
```

### Rendering a file listing

```sh
mq -L . -I raw 'import "tree" | tree::tree_render_paths(["src/main.rs", "src/lib.rs", "src/util/foo.rs", "README.md"])'
```

```
.
├── src
│   ├── main.rs
│   ├── lib.rs
│   └── util
│       └── foo.rs
└── README.md
```

### Building a tree by hand

```sh
mq -L . -I raw 'import "tree" | tree::tree_render(tree::tree_branch("root", [tree::tree_leaf("a"), tree::tree_branch("b", [tree::tree_leaf("c")])]))'
```

```
root
├── a
└── b
    └── c
```

## License

MIT
