# Mason

TODO

# Deploying code

#### Packaging binaries

  - Use [node-pre-gyp](https://github.com/mapbox/node-pre-gyp) for node C++ modules
  - Use [mason](https://github.com/mapbox/mason) for C++ libraries and command line (cli) applications

#### Upgrade libstdc++

For binaries to run properly they will need the latest libstdc++ version installed.

#### Using apt-get directly

```
sudo apt-get install -y software-properties-common python-software-properties || true
sudo add-apt-repository -y ppa:ubuntu-toolchain-r/test
sudo apt-get update -y
sudo apt-get install -y libstdc++-5-dev
```

##### Update vs upgrade?

Note that the above lines use `update` and `install <package name>` not `upgrade`. 

The `apt-get upgrade` command will upgrade every package on your system and does not accept arguments.

Usually what you want is to upgrade a single package. The way to do that is using `update` not `upgrade` like:

```
apt-get update -y
apt-get install -y <package name>
```

See https://linux.die.net/man/8/apt-get for more details.


##### Upgrade libstdc++ On Travis:

```yml
addons:
  apt:
    sources: [ 'ubuntu-toolchain-r-test' ]
    packages: [ 'libstdc++-5-dev' ]
```

##### Upgrade libstdc++ On Circleci:

```yml
dependencies:
     pre:
         - sudo apt-add-repository -y ppa:ubuntu-toolchain-r/test
         - sudo apt-get -y update
         - sudo apt-get -y install libstdc++-5-dev
```
