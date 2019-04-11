# Setup and Getting Started Guide

The applications consists of two main components, which contain setup instructins of their own:

- The *ew-link* repository contains the code to communicate with the charging station
  - written in Python 3.7
  - implements OCPP v1.6
  - to be deployed to Raspberry Pi 3 or similar
  - one instance per charging station
  - can be deployed using docker

- The *ew-origin-frontend* repository contains the code to run the frontend, initalize a new set of blockchain accounts (addresses and private keys) as well as the visualisation of the charging stations.
  - written in Javascript (Node backend, React.js frontend)
  - one instance per organisation
  - runs a blockchain client
  - consists of multiple components which should be deployed using the included docker-compose
    - [kong](https://konghq.com/kong/) routes the API communication inside Docker
    - elasticsearch is used to store off-chain and diagnostic data
    - ew-helper-demo is the backend to ew-origin-frontend and communicates with the blockchain client
    - ew-origin-frontend itself contains the React single page application to provision new devices and onboard new users, the docker-compose for the whole stack can be found here as well