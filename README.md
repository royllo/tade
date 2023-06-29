# Taproot Assets docker environment (Running lnd tapd with a docker-compose)

## Goals

For a newcomer, setting up tapd to play with Taproot Assets is not easy!

Lots of steps are required (Build Lnd, create wallet, setup neutrino, build tapd, certificate paths, set macaroons...) 
and if, like me, you are developing things using Taproot Assets (calling its API, getting data...), you don't want to 
lose too much time on things like that.

With this project, with only one command, you start an LND node (with a configured and unlocked wallet) and Taro (with
RPC called enabled).

You can focus on your project.

## How it works

This project uses docker-compose to start two containers:
- **lnd** : LND node with a configured and unlocked wallet.
- **tapd** : Taproot Assets daemon with RPC enabled.

`lnd` and `tapd` images include the configuration files you can find in `tapd/volumes` and `lnd/volume` directory.

This has been done by changing the Dockerfile of the `lnd` and `tapd` images with the command `COPY volume/ /root/.tapd`
and `COPY volume/ /root/.lnd`.

The tapd image have access to the /root/.lnd directory from the lnd image. This is done by adding the following line in docker-compose.yml:
```
lnd:/root/.lnd
```

When lnd starts, a wallet is configured and unlocked (The password is `C4-t]-#6uV{BVPQ~`).

## How to use it

TODO Set the new name.
- Download the project: `git clone https://github.com/royllo/lnd-taro-with-docker.git`
- Go to the project directory: `cd lnd-taro-with-docker`
- Start LND and Tarod: `docker-compose up`

After some time, you will see this line : `SRVR: Taro Daemon fully active!`

You can now Taro services with a command like this:

```
curl    --header "Grpc-Metadata-macaroon: $(xxd -ps -u -c 1000 ./admin.macaroon)" \
        --insecure https://localhost:8089/v1/taro/assets
```

You can decode a proof with this command:

```
curl    --header "Grpc-Metadata-macaroon: $(xxd -ps -u -c 1000 ./admin.macaroon)" \
        --data @raw_proof \
        --insecure https://localhost:8089/v1/taro/proofs/decode
```


## Wallet configured in LND

The LND password for the wallet is `C4-t]-#6uV{BVPQ~`.

---------------BEGIN LND CIPHER SEED---------------

1. above 2. evoke 3. spoon 4. number
5. inner 6. shoe 7. hire 8. casual
9. usage 10. manual 11. scan 12. name
13. fringe 14. security 15. verb 16. cactus
17. glad 18. fork 19. patch 20. conduct
21. two 22. peace 23. just 24. detect
---------------END LND CIPHER SEED-----------------

Addresses :

- **p2wkh** : tb1q9ep90x63j3n5dqqyn3jjf8kmpzf65tttupudyg
- **np2wkh** : 2NEG6nf1uBHPGf8X9eyQ7pmUw53bnuamxxg

## Tips

### Remove all volumes
```
docker-compose down --volumes
docker volume rm $(docker volume ls -q)
```

### List files inside a docker volume
`docker inspect <volume_name>`
Then you can do an `ls` on the `Mountpoint` path.
`sudo ls /var/lib/docker/volumes/lnd/_data`

### Remove everything docker
`docker stop $(docker ps -qa); docker rm $(docker ps -qa); docker rmi -f $(docker images -qa); docker volume rm $(docker volume ls -q); docker network rm $(docker network ls -q)`



- If you want to remove everything, just
  type .
- You can get information about lnd with the
  command: `docker exec -it lnd /bin/lncli --macaroonpath=/root/.lnd/data/chain/bitcoin/testnet/admin.macaroon getinfo`.
