# Getting start with EOSIO

```sh
gp preview $(gp url 8000)
```

## Create keys



```sh
cleos create key -f talk.txt
cleos create key -f alice.txt
cleos create key -f bob.txt
```

Create account using public key

```sh
# create
cleos create account eosio alice ...

# get
cleos get account alice
```

Deploy

```sh
cleos set code talk ./contract/talk.wasm
cleos set abi talk ./contract/talk.abi

cleos push action talk post '[1000, 0, bob, "First Bob message"]' -p bob@active

cleos get table talk '' message
```
