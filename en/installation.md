# Installing Nu

The best way currently to get Nu up and running is to install from [crates.io](https://crates.io), download pre-built binaries from our [release page](https://github.com/nushell/nushell/releases), build from source, or pulling a pre-built container with Docker.

## Pre-built binaries

You can download Nu pre-built from the [release page](https://github.com/nushell/nushell/releases). Alternatively, if you use [Homebrew](https://brew.sh/) for macOS, you can install the binary by running `brew install nushell`.

## Pre-built docker containers

If you want to pull a pre-built container, you can browse tags for the [nushell organization](https://quay.io/organization/nushell)
on Quay.io. Pulling a container would come down to:

```bash
$ docker pull quay.io/nushell/nu
$ docker pull quay.io/nushell/nu-base
```

Both "nu-base" and "nu" provide the `nu` binary, however nu-base also includes the source code at `/code`
in the container and all dependencies.

Optionally, you can also build the containers locally using the [dockerfiles provided](https://github.com/nushell/nushell/tree/master/docker):
To build the base image:

```bash
$ docker build -f docker/Dockerfile.nu-base -t nushell/nu-base .
``` 

And then to build the smaller container (using a Multistage build):

```bash
$ docker build -f docker/Dockerfile -t nushell/nu .
``` 

Either way, you can run either container as follows:

```bash
$ docker run -it nushell/nu-base
$ docker run -it nushell/nu
/> exit
```

The second container is a bit smaller, if size is important to you.

## Getting Ready

Before we can install Nu, we need to make sure our system has the necessary requirements. Currently, this means making sure we have both the Rust toolchain and local dependencies installed.

### Installing Rust

If we don't already have Rust on our system, the best way to install it via [rustup](https://rustup.rs/). Rustup is a way of managing Rust installations, including managing using different Rust versions. 

Nu currently requires the **nightly** version of Rust. When you first open "rustup" it will ask what version of Rust you wish to install:

```
Current installation options:

   default host triple: x86_64-unknown-linux-gnu
     default toolchain: stable
  modify PATH variable: yes

1) Proceed with installation (default)
2) Customize installation
3) Cancel installation
```

Select option #2 to customize the installation, 

```
Default host triple?
```

Hit enter here to select the default.

```
Default toolchain? (stable/beta/nightly/none)
```

Make sure to type "nightly" here and press enter. This should give this setup:

```
Modify PATH variable? (y/n)
```

You can optionally update your path. This is generally a good idea, as it makes later steps easier.


```
Current installation options:

   default host triple: x86_64-unknown-linux-gnu
     default toolchain: nightly
  modify PATH variable: yes

1) Proceed with installation (default)
2) Customize installation
3) Cancel installation
```

You can see that our default toolchain has now changed to the nightly toolchain. If this sounds a bit risky, don't worry. The Rust compiler is run through a full battery of tests. The nightly compiler is often as reliable as the stable version.

Once we are ready, we press 1 and then enter.  After this point, we can follow the instructions "rustup" gives us and we should have a working Rust compiler on our system.

If you'd rather not install Rust via "rustup", you can also install it via other methods (eg from a package in a Linux distro). Just be sure to install a recent nightly version of the toolchain.

## Dependencies

### Debian/Ubuntu

You will need to install the "pkg-config" and "libssl-dev" package:

```
apt install pkg-config libssl-dev
```

Linux users who wish to use the `rawkey` or `clipboard` optional features will need to install the "libx11-dev" and "libxcb-composite0-dev" packages:

```
apt install libxcb-composite0-dev libx11-dev
```

### macOS

Using [Homebrew](https://brew.sh/), you will need to install the "openssl" and "cmake" using: 

```
brew install openssl cmake
```

## Installing from [crates.io](https://crates.io)

Once we have the dependencies Nu needs, we can install it using the `cargo` command that comes with the Rust compiler.

```
> cargo +nightly install nu
```

That's it!  The cargo tool will do the work of downloading Nu and its source dependencies, building it, and installing it into the cargo bin path so that we can run it.

If you want to install all the features, including some fun optional ones, you can use:

```
> cargo +nightly install nu --all-features
```

For this to work, make sure you have all the dependencies (shown above) on your system.

Once installed, we can run Nu using the `nu` command:

```
$ nu
/home/jonathan/Source> 
```

## Building from source

We can also build our own Nu from source directly from github. This gives us immediate access to the latest Nu features and bugfixes.

```
> git clone https://github.com/nushell/nushell.git
```

Git will clone the main nushell repo for us. From there, we can build and run Nu:

```
> cd nushell
nushell> cargo build --all-features && cargo run --all-features
```

You can also build and run Nu in release mode:

```
nushell> cargo build --release && cargo run --release
```

People familiar with Rust may wonder why we do both a "build" and a "run" step if "run" does a build by default. This is to get around a shortcoming of the new `default-run` option in Cargo, and ensure that all plugins are built, though this may not be required in the future.
