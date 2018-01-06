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

NOTE: To upgrade apt packages you type `apt-get install <package name>` and not `apt-get upgrade <package name>`. In `apt` terms `upgrade` relates to upgrading all packages per https://linux.die.net/man/8/apt-get. This is not what you want. So to upgrade a single package you do as above and run `apt-get upgrade -y` (to get access to new packages) and then `apt-get install <package name>` to upgrade that single package.

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
