This is a minimal working example (mwe)
for the issue described in [#279](https://github.com/LukeMathWalker/cargo-chef/issues/279).

# What should work?

```sh
docker build -t mwe -f Dockerfile.expected .
```

I would expect that `cargo chef cook` on L28 to work since the `rust-toolchain.toml`
is picked up and the toolchain is installed *before* the actual building is done.
`cargo chef prepare` does install the toolchain since the file is present before
running `cargo chef prepare`.

`cargo chef prepare` detects the `rust-toolchain.toml`. This can be verified
by runnning `docker run $ID cat /mwe/recipe.json` where `$ID` is the 
image id after step 10 obtained via `DOCKER_BUILDKIT=0 docker build -t mwe -f Dockerfile.expected .`.

Another thing that bugs me is the fact that as `cargo chef prepare` is run
the toolchain from `rust-toolchain.toml` is unnecessarily installed.
This is not a huge issue since the toolchain shouldn't change often and can be 
mitigated by running `rustup show` before preparing using chef.
I'm not sure if there is a *"nice"* way chef could handle this but it's not that
important and unexpected.
