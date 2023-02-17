# A docker-compose file to build and run LND and Taro

## Goals

For a newcomer, setting up taro is not easy! lots of steps are required (Build Lnd, create wallet, setup neutrino,
build taro, set certificate paths, set macaroons...) and if, like me, you are developing on top of Taro (calling its
API, getting data...), you don't want to lose too much time on things like that.

With this project, with only one command, you start an LND node (with a configured and unlocked wallet) and Taro (with
RPC called enabled).

You can focus on your project and not on the infrastructure.

## Run

- Download the project: `git clone https://github.com/royllo/lnd-taro-with-docker.git`
- Go to the project directory: `cd lnd-taro-with-docker`
- Start LND and Tarod: `docker-compose up`

After some time, you will see this line : `SRVR: Taro Daemon fully active!`

You can now Taro services with a command like this:

```
curl    --header "Grpc-Metadata-macaroon: $(xxd -ps -u -c 1000 ./admin.macaroon)" \
        -k https://localhost:8089/v1/taro/assets
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

- If you want to remove everything, just
  type `docker stop $(docker ps -qa); docker rm $(docker ps -qa); docker rmi -f $(docker images -qa); docker volume rm $(docker volume ls -q); docker network rm $(docker network ls -q)`.
- You can get information about lnd with the
  command: `docker exec -it lnd /bin/lncli --macaroonpath=/root/.lnd/data/chain/bitcoin/testnet/admin.macaroon getinfo`.
