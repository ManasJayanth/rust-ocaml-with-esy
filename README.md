# Using rust libraries with esy

[esy](https://esy.sh) is a package manager for Reason and OCaml. It's however possible to pull-in rust packages, if needed. Support for rust packages isn't natively available, there OCaml ecosystem does seem to bind to rust packages occasionally. If you wish to do so too, then here's how you can do it with esy.

1. Create an esy manifest (`package.json` or `esy.json`) with [esy-rustup](https://github.com/esy-packages/esy-rustup) package.

```json
{
  "dependencies": {
    "esy-rustup": "^1.51.1"
  }
}
```

On newer MacOS, you'll need `1.56` which is not published on NPM. You could use the github repository formula directly with,

```json
  "resolutions": {
    "esy-rustup": "esy-packages/esy-rustup:esy.json#16c0801743a43f86d13f228ff0b2167015ffd2af"
  }
}
```

2. Run `cargo init` to initialize a rust project

3. Run, `esy cargo run` to run the hello world program.

4. Optionally add `#{self.root}/target/debug` to [`exportedEnv`](https://esy.sh/docs/environment/#test-environment-exported-environment) and run the program with `esy x rust-ocaml-with-esy`

```json
{
    "exportedEnv": {
      "PATH": {
        "scope": "global",
        "val": "#{self.root}/target/debug:$PATH"
      }
    }
}
```

## Adding dependencies

`cargo add` doesn't work with esy it hits the network and violates esy's sandbox assumptions. To workaround this,

1. add crate to `Cargo.toml` manually

```toml
[dependencies]
rand = "0.8.5"
```

2. Run `esy cargo vendor`

3. Build the project `esy` (which would run `esy b cargo build`)

