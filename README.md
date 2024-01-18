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

On newer MacOS, `1.75` is the recommended version, which is not published on NPM. You could use the github repository formula directly with,

```json
  "resolutions": {
    "esy-rustup": "esy-packages/esy-rustup:esy.json#bd83f59efa55da39ba49b155d867e8ae55e3df1"
  }
}
```

2. Run `cargo init` to initialize a rust project

3. Run, `esy cargo run` to run the hello world program.

4. If you're using [`ocaml-rust`](https://docs.rs/ocaml/latest/ocaml/#:~:text=ocaml%2Drs%20is%20a%20library,interact%20with%20the%20OCaml%20runtime), you'll also need to set `OCAML_WHERE_PATH` because of how `ocaml-rust` configures ocaml toolchain.

```json
  "esy": {
    "buildEnv": {
      "OCAML_WHERE_PATH": "#{ocaml.lib}/ocaml",
    },
  }
```

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

