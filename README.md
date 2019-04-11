# Setup and Getting Started Guide

## Setup

For conviencie this repository contains the three git repositories which are required to run the ev charging blockchain application as git submodules. Clone this repository recursively to obtain the submodules:

```sh
git clone --recursive git@github.com:energywebfoundation/elocity-setup-docs.git
```

If you did a non recursive clone already, obtain the submodules by running:

```sh
git submodule init
git submodule update
```

## Getting Started

### First run

Start the application using `sudo docker-compose up` from inside *ew-origin-frontend*. Let docker-compose settle, then restart it to pickup the db initalisation which occures on first run. Compose binds to port 80, therefore you need to run it with elevated priviliges.

Open konga (the frontend to kong) by navigating to [http://localhost:1337](http://localhost:1337). Make sure to input a syntactically correct email address and strong password for the admin account. Otherwise the registration will fail.

Sign-in and create a new Kong connection. Use any descriptive name and the Admin URL: `http://kong:8001`.

Now setup a service and route for the frontend. Click on *Services* on the left side and *Add a new service*. Enter `Frontend` as the name and `http://ew-origin-frontend` for url. Keep all the other values at their defaults and press *Submit service* to create the new service.
You'll see a list containing the new service. Click on its name to configure it, then go to routes and *Add route*. These routes will allow you to reach the frontend inside docker from your host.

To reach the frontend with the url *http://frontend* enter `frontend` next to Hosts and press enter. Give the route a name and *Submit changes*.

Repeat the service and route setup for elasticsearch.

Service:

- Url: http://elasticsearch:9200

Route:

- Hosts: es-kong

Make sure that the domain names resolve to the IP address of the host where kong is running. E.g. add these line to your */etc/hosts* on your local development machine:

```sh
127.0.0.1    frontend
127.0.0.1    es-kong
```

#### Authentication

You can protect the routes with authentication by adding a *Basic Auth* plugin. For elasticsearch use the user name `read` and make sure to create a file called *.env* with the following content in ew-origin-frontend. You need to rebuild the ew-origin-frontend conatiner afterwards.

```
REACT_APP_KONG_ES_PW=secureESpw
```

## Browser Setup
Required for each browser you're using to access the frontend: Install and setup the browser extension [MetaMask](https://metamask.io/). Point metamask to the machine which is running the Docker containers. E.g. localhost:8545 for your local machine.

### Consecutive runs

Once you started the application using `docker-compose up` from inside *ew-origin-frontend* navigate to [http://frontend](http://frontend). After installing and unlocking MetaMask you see the Onboarding and Device Creator interface. Start by creating an asset of type Admin. The private key will be visible in the form field. Repeat the step for the Matcher account.

Create a new producing asset (producing means it's an energy source like a charging station).

Although all the values you enter are stored in browser local storage to persis them between session, it's wise to download your generated private keys now. Press download to retrieve a json file with both private keys.

When you're done press on deploy to send the configuration you just created to the backend which will then perform all the neccessary steps to deploy a new set of blockchain contracts and setup the assets. This takes a few moments. After completion you'll see the top level address of your contracts already filled in at the very top. Press on open now.

If everything was setup correctly you see an interface with a table of all your producing assets (charging stations).

## Structure

The applications consists of two main components, which contain setup instructins of their own:

- The *ew-link* repository contains the code to communicate with the charging station
  - written in Python 3.7
  - implements OCPP v1.6
  - to be deployed to Raspberry Pi 3 or similar
  - one instance per charging station
  - can be deployed using docker
  - read/writes to the blockchain (on-chain) and a local elasticsearch instance (off-chain)

- The *ew-origin-frontend* repository contains the code to run the frontend, initalize a new set of blockchain accounts (addresses and private keys) as well as the visualisation of the charging stations.
  - written in Javascript (Node backend, React.js frontend)
  - to be deployed on a x86 server or workstation
  - one instance per organisation
  - runs a blockchain client
  - consists of multiple components which should be deployed using the included docker-compose
    - [kong](https://konghq.com/kong/) routes the API communication inside Docker
    - elasticsearch is used to store off-chain and diagnostic data
    - ew-helper-demo is the backend to ew-origin-frontend and communicates with the blockchain client
    - ew-origin-frontend itself contains the React single page application to provision new devices and onboard new users, the docker-compose for the whole stack can be found here as well