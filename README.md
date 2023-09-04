# mediasoup-demo v3

A demo application of [mediasoup](https://mediasoup.org) **v3**.

Try it online at https://v3demo.mediasoup.org

https://github.com/daily-co/mediasoup-sandbox/tree/main/single-page

## Running on AWS EC2 instances

1. Make sure that your instance security group allows inbound TCP to
   port 3000, inbound UDP to ports 40000-49999, and all outbound traffic.

2. On AWS Linux you'll need to install a newer version of g++ than is
   included in the Development Tools package group.

```
# from-scratch install on AWS Linux

sudo yum install git
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.32.0/install.sh | bash
. ~/.nvm/nvm.sh
nvm install 11
sudo yum groupinstall "Development Tools"
sudo yum install gcc72-c++
git clone https://github.com/daily-co/mediasoup-sandbox.git
cd mediasoup-sandbox/
npm install
```

3. Make a `listenIps` entry with `ip` set to the instance's private IP
   address, and `announcedIp` set to the instance's public IPv4 address.

## Resources

* mediasoup website and documentation: [mediasoup.org](https://mediasoup.org)
* mediasoup support forum: [mediasoup.discourse.group](https://mediasoup.discourse.group)


## Configuration via query parameters

By adding query parameters into the URL you can set certain settings of the application:

| Parameter          | Type    | Description          | Default Value |
| ------------------ | ------- | -------------------- | ------------- |
| `roomId`           | String  | Id of the room      | Autogenerated  |
| `displayName`      | String  | Display name of your participant | Autogenerated |
| `handlerName`      | String  | Handler name of the mediasoup-client `Device` instance | Autodetected |
| `forceTcp`         | Boolean | Force RTC (audio/video/datachannel) over TCP instead of UDP | `false` |
| `produce`          | Boolean | Enable sending of audio/video | `true`  |
| `consume`          | Boolean | Enable reception of audio/video | `true` |
| `datachannel`      | Boolean | Enable DataChannels | `true` |
| `forceVP8`        | Boolean | Force VP8 codec for webcam and screen sharing | `false` |
| `forceH264`        | Boolean | Force H264 codec for webcam and screen sharing | `false` |
| `forceVP9`        | Boolean | Force VP9 codec for webcam and screen sharing | `false` |
| `enableWebcamLayers` | Boolean | Enable simulcast or SVC for webcam | `true` |
| `enableSharingLayers` | Boolean | Enable simulcast or SVC for screen sharing | `true` |
| `webcamScalabilityMode` | String | `scalabilityMode` for webcam | 'L1T3' for VP8/H264 (in each simculast encoding), 'L3T3_KEY' for VP9 |
| `sharingScalabilityMode` | String | `scalabilityMode` for screen sharing | 'L1T3' for VP8/H264 (in each simculast encoding), 'L3T3' for VP9 |
| `numSimulcastStreams` | Number | Number of streams for simulcast in webcam and screen sharing | 3 |
| `info`             | Boolean | Display detailed information about media transmission | `false` |
| `faceDetection`    | Boolean | Run face detection | `false` |
| `externalVideo`    | Boolean | Send an external video instead of local webcam | `false` |
| `e2eKey`           | String | Key for media E2E encryption/decryption (just works with some OUPS and VP8 codecs) | |
| `consumerReplicas` | Number | Create artificial replicas of yourself and receive their audio and video (not displayed in the UI) | 0 |


## Installation

* Clone the project:

```bash
$ git clone https://github.com/versatica/mediasoup-demo.git
$ cd mediasoup-demo
$ git checkout v3
```

* Ensure you have installed the [dependencies](https://mediasoup.org/documentation/v3/mediasoup/installation/#requirements) required by mediasoup to build.

* Set up the mediasoup-demo server:

```bash
$ cd server
$ npm install
```

* Copy `config.example.js` as `config.js` and customize it for your scenario:

```bash
$ cp config.example.js config.js
```

**NOTE:** To be perfectly clear, "customize it for your scenario" is not something "optional". If you don't set proper values in `config.js` the application **won't work**. You must configure a tls certificate.

* Set up the mediasoup-demo browser app:

```bash
$ cd app
# For node 16
$ npm install
# For node 18, use legacy peer dependencies
$ npm install --legacy-peer-deps  
```


## Run it locally

* Run the Node.js server application in a terminal:

```bash
$ cd server
$ npm start
```

* In a different terminal build and run the browser application:

```bash
$ cd app
$ npm start
```

If you configured a self-signed tls certificate, and receive wss: connection errors, open the wss: url with https: protocol to accept the cert and allow wss: connections in your browser.

* Enjoy.


## Deploy it in a server

* Globally install `gulp-cli` NPM module (may need `sudo`):

```bash
$ npm install -g gulp-cli
```

* Build the production ready browser application:

```bash
$ cd app
$ gulp dist
```

* Upload the entire `server` folder to your server and make your web server (Apache, Nginx, etc) expose the `server/public` folder.

* Edit your `server/config.js` with appropriate settings (listening IP/port, logging options, **valid** TLS certificate, etc).

* Within your server, run the Node.js application by setting the `DEBUG` environment variable according to your needs ([more info](https://mediasoup.org/documentation/v3/mediasoup/debugging/)):

```bash
$ DEBUG="*mediasoup* *ERROR* *WARN*" node server.js
```
* If you wish to run it as daemon/service you can use [pm2](https://www.npmjs.com/package/pm2) process manager. Or you can dockerize it among other options.

* The Node.js application exposes an interactive terminal. When running as daemon (in background) the host administrator can connect to it by entering into the `server` folder and running:

```bash
$ npm run connect
```

## Run mediasoup server with Docker

* Required environment variables: [server/DOCKER.md](server/DOCKER.md).
* Build the Docker image: [server/docker/build.sh](server/docker/build.sh).
* Run the Docker image: [server/docker/run.sh](server/docker/run.sh).

```
$ cd server
$ docker/build.sh
$ MEDIASOUP_ANNOUNCED_IP=192.168.1.34 ./docker/run.sh
```

#### Considerations for [config.js](server/config.example.js)

* Make sure [https.listenIp](server/config.example.js#L20) is set to `0.0.0.0`.
* Make sure [TLS certificates](server/config.example.js#L24) reside in `server/certs` directory with names `fullchain.pem` and `privkey.pem`.
* The default mediasoup port range is just 2000-2020, which is not suitable for production. You should increase it, however you should then run the container in `network="host"` mode.


## Authors

* Iñaki Baz Castillo [[website](https://inakibaz.me)|[github](https://github.com/ibc/)]
* José Luis Millán Villegas [[github](https://github.com/jmillan/)]


## License

MIT