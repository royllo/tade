# A docker-compose file to build and run LND and Taro

## What's inside ?

The `docker-compose.yaml` runs:

- A volume that shares the `.lnd` directory from the lnd image.
- LND node (neutrino) with a wallet already configured and unlocked.
- A Taro server connecting to the previous LND image.

## Configure and build images

To create the two images (lnd & taro):

```
docker build taro/lnd/ -t lnd
docker build taro/taro/ -t taro
```

In the `lnd` directory, you will find the configuration here: `lnd/volume/lnd.conf`.
In the `taro` directory, you will find the configuration here: `lnd/taro.conf`.

## Run separately

To make it run, you first need to create a common volume to share data, then run lnd, then create a wallet and run taro:

```
docker rm $(docker ps -aq --filter name=lnd)
docker rm $(docker ps -aq --filter name=taro)
docker volume create lnd

docker run --name lnd -v lnd:/root/.lnd -p 9735:9735 -p 10009:10009 lnd:latest
docker run --name taro -v lnd:/root/.lnd -p 10029:10029 -p 8089:8089 taro:latest
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
