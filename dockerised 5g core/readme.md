# Dockerised Open5GS and gNB Setup Instructions

## 1. Add Configuration Files

Place the following files in the `open5gs` directory:

- `open5gs-5gc.yml`: This is the Docker Compose configuration file for running the Open5GS 5G Core.
- `open5gs.env`: This file contains subscriber-specific parameters such as:
  - `IMSI`
  - `K`
  - `OPc`

## 2. Start the Open5GS Container

Navigate to the `open5gs` directory and start the container.

## 3. Start the gNB

Once the Open5GS container is running successfully, you can start the gNB using the provided gNB configuration file.
