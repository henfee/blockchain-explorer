# Hyperledger Explorer

Hyperledger Explorer is a simple, powerful, easy-to-use, highly maintainable, open source browser for viewing activity on the underlying blockchain network.

## Directory Structure
```
├── app                    fabric GRPC interface
├── artifacts              
├── db			   the mysql script and help class
├── explorer_client        Web Ui
├── listener               websocket listener
├── metrics                metrics about tx count per minute and block count per minute
├── service                the service 
├── socket		   push real time data to front end
├── timer                    
└── utils                    
```


## Requirements


Following are the software dependencies required to install and run hyperledger explorer 
* nodejs 6.9.x (Note that v7.x is not yet supported)
* mysql 5.7 or greater


Hyperledger Explorer works with Hyperledger Fabric 1.0.  Install the following software dependencies to manage fabric network.
* docker 17.06.2-ce [https://www.docker.com/community-edition]
* docker-compose 1.14.0 [https://docs.docker.com/compose/]

## Database setup
Run the database setup scripts located under `db/fabricexplorer.sql`

`mysql -u<username> -p < db/fabricexplorer.sql`

## set fabric docker env

You can setup your own network using [Build your network](http://hyperledger-fabric.readthedocs.io/en/latest/build_network.html) tutorial from Fabric.

Here is a sample network configuration to start with

1. `git clone https://github.com/onechain/fabric-docker-compose-svt.git`
2. `mv fabric-docker-compose-svt $GOPATH/src/github.com/hyperledger/fabric/examples/`
3. `cd $GOPATH/src/github.com/hyperledger/fabric/examples/fabric-docker-compose-svt`
4. `./download_images.sh`
5. `./start.sh`


## start fabric-explorer

1. `git clone https://github.com/hyperledger/blockchain-explorer.git`
2. `cd blockchain-explorer/fabric-explorer`
3. `mkdir artifacts`
4. `cp -r $GOPATH/src/github.com/hyperledger/fabric/examples/fabric-docker-compose-svt/crypto-config artifacts/crypto-config/`

5. modify config.json,set channel,mysql,tls (if you use tls communication, please set  enableTls  true ,if not set false) 
```json
 "channelsList": ["mychannel"],
 "enableTls":true, 
 "mysql":{
      "host":"172.16.10.162",
      "database":"fabricexplorer",
      "username":"root",
      "passwd":"123456"
   }
```

5. modify app/network-config.json or app/network-config-tls.json(if you use tls communication) 

```json
 {
	"network-config": {
		"orderer": [{
			"url": "grpc://112.124.115.82:7050",
			"server-hostname": "orderer0.example.com"
		},{
			"url": "grpc://112.124.115.82:8050",
			"server-hostname": "orderer1.example.com"
		},{
			"url": "grpc://112.124.115.82:9050",
			"server-hostname": "orderer2.example.com"
		}],
		"org1": {
			"name": "peerOrg1",
			"mspid": "Org1MSP",
			"ca": "http://112.124.115.82:7054",
			"peer1": {
				"requests": "grpc://112.124.115.82:7051",
				"events": "grpc://112.124.115.82:7053",
				"server-hostname": "peer0.org1.example.com"
			},
			"peer2": {
				"requests": "grpc://112.124.115.82:8051",
				"events": "grpc://112.124.115.82:8053",
				"server-hostname": "peer1.org1.example.com"
			},
			"admin": {
				"key": "/artifacts/crypto-config/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/keystore",
				"cert": "/artifacts/crypto-config/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/signcerts"
			}
		},
		"org2": {
			"name": "peerOrg2",
			"mspid": "Org2MSP",
			"ca": "http://112.124.115.82:8054",
			"peer1": {
				"requests": "grpc://112.124.115.82:9051",
				"events": "grpc://112.124.115.82:9053",
				"server-hostname": "peer0.org2.example.com"
			},
			"peer2": {
				"requests": "grpc://112.124.115.82:10051",
				"events": "grpc://112.124.115.82:10053",
				"server-hostname": "peer1.org2.example.com"
			},
			"admin": {
				"key": "/artifacts/crypto-config/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/keystore",
				"cert": "/artifacts/crypto-config/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/signcerts"
			}
		}
	}
}
```

6. `npm install`
7. `./start.sh`

Launch the URL http://localhost:8080 on a browser.

## Screenshots

This is how the fabric-explorer looks like,

![Fabric Explorer](https://github.com/xspeedcruiser/explorer-images/raw/master/blockchain-exp1.png)

![Fabric Explorer](https://github.com/xspeedcruiser/explorer-images/raw/master/blockchain-exp.png)

![Fabric Explorer](https://github.com/xspeedcruiser/explorer-images/raw/master/blockchain-exp3.png)

## SIMPLE REST-API

I provide a simple rest-api

```
//get block info
curl -X POST  http://localhost:8080/api/block/json -H 'cache-control: no-cache' -H 'content-type: application/json' -d '{
    "number":"${block number}"
}'

//get transcation JOSN
curl -X POST  http://localhost:8080/api/tx/json -H 'cache-control: no-cache' -H 'content-type: application/json' -d '{
    "number":"${ Tx hex }"
}'


//get peer status
curl -X POST  http://localhost:8080/api/status/get -H 'cache-control: no-cache' -H 'content-type: application/json' -d ''

//get chaincode list
curl -X POST  http://localhost:8080/chaincodelist -H 'cache-control: no-cache' -H 'content-type: application/json' -d ''

```