Builds a Docker image from Bitcoin, which allows running `bitcoind` or `bitcoin-cli` inside a container.

Internal Melotools infrastructure tracks new Bitcoin releases and triggers CI jobs on this repo, to publish the images to [DockerHub](https://hub.docker.com/repository/docker/melotools/bitcoin)

## Supported tags and respective `Dockerfile` links
* `latest` ([Dockerfile](https://github.com/melotools/bitcoin/blob/master/Dockerfile))

## Usage

In order to switch from user and password based authentication (using `-rpcuser` and `-rpcpassword`) to `-rpcauth` it is worth mentioning, that the docker image needs to be configured in a certain way (using environment variables):
* `RPC_AUTH` is set and is not `""`.
  - `-rpcauth=$RPC_AUTH` is used.
  - `RPC_USER` and `RPC_PASSWORD` are ignored, even if set.
* `RPC_AUTH` is not set or is `""` **and** `RPC_USER` **and** `RPC_PASSWORD` are set and are not `""`.
  - `-rpcuser=$RPC_USER` and `-rpcpassword=$rPC_PASSWORD` will be used.

A few more words about `-rpcauth`:
* Create a value for `-rpcauth` (`RPC_AUTH`):
  - Run the script `rpcauth.py` to create credentials.
  - The script can be found in the official bitcoin github repository:
  ```
      share/rpcauth/rpcauth.py
      # Also have a look at the README file
      share/rpcauth/README.md
  ```
  - The result will be a string in the following format:
    + `<username>:<salt>$<password_hmac>`
    + This is what `RPC_AUTH` is to be configured with.
  - Additionally you will get a `<password>` which is to be used to configure the clients connecting to the bitcoind RPC.
    + The `<password>` is used to create `<password_hmac>`.

---

Not specifying a host port in `-p <host_port>:<container_port>` docker will automatically assign a free port on the host.

```
docker run --rm -d -p 8332 -v <path/to/and/including/wallets>:/bitcoin melotools/bitcoin -datadir=/bitcoin
```

However, this only works for the common Bitcoin network ports:
* `8332`
* `8333`
* `18332`
* `18333`

