# conanw

A stupid simple [conan](https://conan.io) installer / runner, inspired by [gradlew](https://docs.gradle.org/current/userguide/gradle_wrapper.html).

## Why?

Isolated development environments keep me sane. conan can already manage project level dependencies like cmake, ninja, or protoc.

But how do we install conan itself in an isolated way? The conan documentation ["strongly recommends" using virtualenvs](https://docs.conan.io/en/latest/installation.html) to isolate conan from other python dependencies. Sure, we could use docker but I find it cumber to wrap command line that interact with the local filesystem.

Virualenvs should be very familiar to python developers, but conan is a primarily C/C++ tool, and C/C++ developers should not be expected to know about python dependency management.

This little script aims to "do the right thing" by installing conan in a virtualenv, without needing to expose others to the concept.

## Warning

This mini-project is very young and may be a silly idea in general.

## Quick Start

Download the `conanw` script and add it to your repository.

```bash
curl -O https://raw.githubusercontent.com/amrox/conanw/main/conanw 
chmod +x conanw
```

The first time you run `conaw` it will download and install an isolated conan. Then it will act as a simple wrapper for that conan install.

```bash
❯ ./conanw                                                                                                                                                                                 ~/Projects/conanw
conanw: Creating a new python virtual env at /Users/amrox/Projects/conanw/.conanw-venv
conanw: Installing conan ...

   <conan install output>

conanw: conan active at /Users/amrox/Projects/conanw/.conanw-venv/bin/conan

Consumer commands
  install    Installs the requirements specified in a recipe (conanfile.py or conanfile.txt).
  config     Manages Conan configuration.

  <rest of conan help ouput>
```

Subsequent runs of `conanw` will just delegate directly to conan.

## Basic Usage

Generally you want to use `conanw` just like conan:

```bash
./conanw frogarian
```

There are a few extra features though.

### Install conan without actually running conan

```bash
./conanw --cw-check
```

### Re-install conan

Removes the existing virtualenv and re-installs conan.

```bash
./conanw --cw-reinstall
```

### Get information about the conan install

Print the path to our conan:

```bash
./conanw --cw-path
/Users/amrox/Projects/conanw/.conanw-venv/bin/conan
```

Print the dir which contains our conan. Useful to add it to your PATH

```bash
./conanw --cw-dir
/Users/amrox/Projects/conanw/.conanw-venv/bin
```

```bash
export PATH=$(./conanw --cw-dir):$PATH
which conan
/Users/amrox/Projects/conanw/.conanw-venv/bin/conan
```

See also: [direnv integration](#direnv-integration)

### Controlling the conan version

By default, conanw will just grab the latest version of conan. If you want a specific version (or version range), you can supply a [version specifier](https://www.python.org/dev/peps/pep-0440/#version-specifiers) via the `CONANW_CONAN_VERSION_SPEC` environment variable. This value is passed directly to `pip`.

Examples:

Exact version:

```bash
CONANW_CONAN_VERSION_SPEC="==1.32.0" ./conanw --cw-check
```

Range:

```bash
CONANW_CONAN_VERSION_SPEC=">=1.32.0,<2" ./conanw --cw-check
```

The `CONANW_CONAN_VERSION_SPEC` variable can also be read from an optional [`.conanw.conf`](examples/conf/.conanw.conf) file:

```bash
CONANW_CONAN_VERSION_SPEC=">=1.32.0,<2"
```

## Requirements & Compatibility

`conanw` assumes you have `python` with the `venv` module. This is built in to many python distributions. On Ubuntu you may have to do:

```bash
sudo apt-get install python3-venv
```

`conanw` has been lightly testing in the following environments:

- macOS 10.5
- Ubuntu 18.04

## Tips and Tricks

### direnv integration

I'm a huge fan of [direnv](https://direnv.net) and `conanw` is designed to work well with it.

See example [.envrc](examples/direnv/.envrc)