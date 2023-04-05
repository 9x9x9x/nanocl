# Developer documentation

Step in a unknow project can be difficult, even when you have some experiences.<br />
This documentation will help you to setup `Nanocl` in development.<br />
Note: `Nanocl` heavily use [ntex](https://ntex.rs) as **client** and **server**


## 📙 Table of Contents

* [📁 Project Structure](#-project-structure)
* [💾 Installation](#-installation)
  * [🐧 Ubuntu](#-ubuntu)
  * [🦀 Rust](#-rust)
* [🏃 Running](#-running)
* [👌 Usefull Command](#-usefull-command)


## 📁 Project Structure

`Nanocl` is using a **mono repository structure**.<br />

```sh
bin
├── ctrl-dns # Controller DNS
│   ├── dnsmasq # Source to build dnsmasq container image
│   └── src # Rust source code
├── ctrl-proxy # Controller PROXY
│   ├── nginx # Source to build nginx container image
│   │   └── html
│   ├── src # Rust source code
│   └── tests # Configuration to tests
├── nanocl # Nanocl CLI
│   └── src # Rust source code
│       ├── commands # Function that execute commands
│       ├── models # Data structure used in the project
│       └── utils # Utils functions
└── nanocld # Nanocl DAEMON REST API
    ├── migrations # Container SQL migration generated with diesel
    │   ├── 00000000000000_diesel_initial_setup
    │   ├── 2022-05-20-134629_create_namespaces
    │   ├── 2022-06-17-122356_create_cargos
    │   ├── 2022-08-04-214925_create_nodes
    │   ├── 2023-01-15-121652_resources
    │   ├── 2023-02-17-193350_metrics
    │   └── 2023-03-10-234850_vms
    ├── specs # Configuration the daemon will apply at runtime
    │   └── controllers # Controller configurations the daemon will apply at runtime
    └── src # Rust source code
        ├── models # Data structure used in the project
        ├── repositories # Functions to make SQL request
        ├── services # Function to accepts http request
        ├── subsystem # Function every runtime to ensude the default state is setup
        └── utils # Utils functions
crates # Libraries
├── nanocld_client # A nanocld client
│   └── src # The rust source code
└── nanocl_stubs # Shared data structure mostly used as input and output of out DAEMON
    └── src # The rust source code
```


## 💾 Installation

Clone the repository:

```sh
git clone https://github.com/nxthat/nanocl
```

To build and run `Nanocl` you will need these dependencies

* [rust](https://www.rust-lang.org) >= 1.67
* [docker](https://www.docker.com) >= 1.41
* gcc
* make
* libpq-dev
* openssl-dev


### 🐧 Ubuntu

If you are running on ubuntu there is some scripts to help you install dependencies:

```sh
./scripts/ubuntu.deps.sh
```

If you need docker:

```sh
./scripts/install_docker.ubuntu.sh
```


### 🦀 Rust

To install rust

```sh
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Then you can install rust devtools required to run `Nanocl`

```sh
./scripts/rust.deps.sh
```


## 🏃 Running

You can run `Nanocl` in multiple way

First you need to start a daemon, the daemon need to have right to access to docker.<br />
The daemon is our principal **REST API** and will start the required components at runtime.

Make sure your are in docker group if it's not you can add it like this

```sh
sudo usermod -aG docker $USER
newgrp docker
```

Knowing that `Nanocl Daemon` will create a unix socket at `/run/nanocl/nanocl.sock`
I make sure the folder `/run/nanocl` exists

```sh
sudo mkdir /run/nanocl
sudo chmod 777 -R /run/nanocl
```

Before running `Nanocl` we will need to download and build some docker images:

```sh
./scripts/install_dev_image.sh
```

Finally we can start the daemon.
You can do it in multiple way :

* Using cargo make

  ```sh
  cargo make dev # Run the daemon (the daemon will start required services)
  ```

* Using cargo

  ```sh
  cargo run --no-default-features --features dev --bin nanocld
  ```

* Using cargo watch

  ```sh
  cargo watch -x "run --no-default-features --features dev --bin nanocld"
  ```

Once started a swagger should be available on [http://localhost:8585/explorer](http://localhost:8585/explorer).


<div align="center">
  <img src="./swagger.png" />
</div>


Note that a *env variable* could be passed to change the port, it's hardcoded for now.<br />
It's could be a nice an easy first issue and pull request if you are read till there :).


Now you can run the CLI:

* Using cargo make

  ```sh
  cargo make run-cli version
  ```

* Using cargo

  ```sh
  cargo run --bin nanocl version
  ```

## 👌 Usefull Command

Some usefull command to know:


* lsns - list namespaces
  ```sh
  lsns
  ```

* nsenter - run program in different namespaces
  ```sh
  sudo nsenter -t 12267 -n ss -ltu
  ```

* Generate a nanocld client
  ```sh
  docker run --rm -v $(pwd):/local openapitools/openapi-generator-cli generate -g rust -i /local/specs/v1/swagger.json -o /local/client
  ```

* Generate ssl cert from certbot
  ```sh
  nanocl exec system-nano-proxy -- certbot --nginx --email email@email.com --agree-tos -d your-domain.com
  ```
