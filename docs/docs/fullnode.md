---
hide:
    - side_toc
---

# Bitcoin full node

El primer paso para utilizar los Bitcoin Ordinals es tener un [nodo completo](https://bitcoin.org/en/full-node) conectado a la red de Bitcoin, el cual sirve para observar todas las transacciones y bloques que se han almacenado en el blockchain de Bitcoin

En este documento explicaré cómo montar un nodo completo en **linux**

## Requerimientos

* Computadora de escritorio, laptop o [Raspberry Pi](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/)
* Bitcoin Core
* Disco duro externo de 1 TB
* Conexión a internet
* [Paciencia](https://xkcd.com/303/)

El blockchain de Bitcoin pesa más de [500 GB](https://blockchair.com/bitcoin/charts/blockchain-size) y contiene mas de [800 millones](https://blockchair.com/bitcoin/charts/total-transaction-count) de transacciones, por este motivo el proceso de montar un nodo completo es extremadamente lento y deberás esperar horas o incluso días para sincronizarte con la red de Bitcoin

## Configuración

Nuestro nodo utilizará el software que mantiene el equipo del **Bitcoin Core**, existen otras opciones para configurar un nodo completo pero actualmente el proyecto de Bitcoin Ordinals solo soporta este software

La configuración de este software se debe almacenar en la carpeta de configuración del Bitcoin Core que varía dependiendo del sistema operativo que estes utilizando

| OS | Carpeta de configuración |
|-------------------|--------------------------|
| Windows | %APPDATA%\Bitcoin\bitcoin.conf |
| Linux | $HOME/.bitcoin/bitcoin.conf |
| Mac | $HOME/Library/Application Support/Bitcoin/bitcoin.conf |

En linux debes crear una carpeta llamada **.bitcoin** que contenga un archivo llamado **bitcoin.conf**

```
$ cd /home/cvasqxz
$ mkdir .bitcoin
$ touch .bitcoin/bitcoin.conf
```

La configuración que usé para mi nodo es la siguiente:

* daemon: Ejecutar Bitcoin Core en modo daemon
* txindex: Almacenar todo el registro de transacciones
* server: Activar servidor JSON-RPC
* blockfilterindex: Crear filtro de indices de bloques
* peerblockfilters: Crear filtro de conexiones
* coinstatsindex: Crear filtro de estadísticas
* datadir: **Ruta de mi disco duro externo**

Esta configuración debe ser escrita en el archivo bitcoin.conf de la siguiente manera:

```
daemon=1
txindex=1
server=1

blockfilterindex=1
peerblockfilters=1
coinstatsindex=1

datadir=/media/cvasqxz/disco_externo
```

Con esta configuración Bitcoin core se ejecutará como proceso, tendrá una conexión abierta para recibir ordenes de otro software (JSON-RPC), almacenará todo el historial de transacciones de Bitcoin en tu disco duro externo y podrás [ayudar a lightning network](https://www.reddit.com/r/lightningnetwork/comments/mgaosp/running_a_full_node_configure_compact_filters_to/) con la creación de los filtros de block index, peer block y coin stats

## Sincronizando Bitcoin Core

Ahora que tu nodo está configurado debes ejecutar el software de Bitcoin Core, para esto debemos descargar y descomprimir los archivos

Puedes descargar Bitcoin Core desde la [página oficial](https://bitcoincore.org/en/download/) del proyecto

```
$ cd /home/cvasqxz

$ wget -nv https://bitcoincore.org/bin/bitcoin-core-24.0.1/bitcoin-24.0.1-x86_64-linux-gnu.tar.gz
2023-02-13 17:09:56 URL:https://bitcoincore.org/bin/bitcoin-core-24.0.1/bitcoin-24.0.1-x86_64-linux-gnu.tar.gz [46384770/46384770] -> "bitcoin-24.0.1-x86_64-linux-gnu.tar.gz" [1]

$ tar -vxf bitcoin-24.0.1-x86_64-linux-gnu.tar.gz 
bitcoin-24.0.1/
bitcoin-24.0.1/README.md
bitcoin-24.0.1/bin/
bitcoin-24.0.1/bin/bitcoin-cli
bitcoin-24.0.1/bin/bitcoin-qt
bitcoin-24.0.1/bin/bitcoin-tx
bitcoin-24.0.1/bin/bitcoin-util
bitcoin-24.0.1/bin/bitcoin-wallet
bitcoin-24.0.1/bin/bitcoind
bitcoin-24.0.1/bin/test_bitcoin
bitcoin-24.0.1/bitcoin.conf
bitcoin-24.0.1/include/
bitcoin-24.0.1/include/bitcoinconsensus.h
bitcoin-24.0.1/lib/
bitcoin-24.0.1/lib/libbitcoinconsensus.so
bitcoin-24.0.1/lib/libbitcoinconsensus.so.0
bitcoin-24.0.1/lib/libbitcoinconsensus.so.0.0.0
bitcoin-24.0.1/share/
bitcoin-24.0.1/share/man/
bitcoin-24.0.1/share/man/man1/
bitcoin-24.0.1/share/man/man1/bitcoin-cli.1
bitcoin-24.0.1/share/man/man1/bitcoin-qt.1
bitcoin-24.0.1/share/man/man1/bitcoin-tx.1
bitcoin-24.0.1/share/man/man1/bitcoin-util.1
bitcoin-24.0.1/share/man/man1/bitcoin-wallet.1
bitcoin-24.0.1/share/man/man1/bitcoind.1
bitcoin-24.0.1/share/rpcauth/
bitcoin-24.0.1/share/rpcauth/README.md
bitcoin-24.0.1/share/rpcauth/rpcauth.py
```

Los dos archivos que utilizaremos son **bitcoind**, que es el proceso que se conectará a la red de Bitcoin, y **bitcoin-cli**, que permite comunicarse con el proceso que genera el bitcoind

Si la configuración del archivo bitcoin.conf se realizó correctamente te aparecerá el mensaje **"Bitcoin Core starting"** al iniciar el proceso del bitcoind y se generará un archivo de logging llamado **debug.log** (en tu disco duro externo) que te muestra lo que está pasando con tu nodo

```
$ ./home/cvasqxz/bitcoin-24.0.1/bin/bitcoind        
Bitcoin Core starting

$ less /media/cvasqxz/disco_externo/debug.log
2023-02-13T20:17:15Z Bitcoin Core version v24.0.1 (release build)
2023-02-13T20:17:15Z Using the 'x86_shani(1way,2way)' SHA256 implementation
2023-02-13T20:17:15Z Using RdSeed as an additional entropy source
2023-02-13T20:17:15Z Using RdRand as an additional entropy source
2023-02-13T20:17:15Z Default data directory /home/cvasqxz/.bitcoin
2023-02-13T20:17:15Z Using data directory /media/cvasqxz/disco_externo
2023-02-13T20:17:15Z Config file: /home/cvasqxz/.bitcoin/bitcoin.conf
2023-02-13T20:17:15Z Config file arg: blockfilterindex="1"
2023-02-13T20:17:15Z Config file arg: coinstatsindex="1"
2023-02-13T20:17:15Z Config file arg: daemon="1"
2023-02-13T20:17:15Z Config file arg: peerblockfilters="1"
2023-02-13T20:17:15Z Config file arg: server="1"
2023-02-13T20:17:15Z Config file arg: testnet="1"
2023-02-13T20:17:15Z Config file arg: txindex="1"
:
```

Ahora debes esperar a que se sincronice tu nodo con la red de Bitcoin, esto puede tomar entre 8 y 24 horas en una computadora moderna y hasta un par de semanas si estas usando una Raspberry Pi

## Utilizando tu nodo

