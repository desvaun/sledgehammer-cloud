# SledgeHammer Studio

SledgeHammer Studio provides a containerized environment for deploying and running SledgeHammer physical design workflows on cloud infrastructure.

This repository contains the Docker and cloud configuration used to build the SledgeHammer worker environment. SledgeHammer itself is maintained separately in the Hammer repository.

## Requirements

- Docker
- Git
- Network access to the SledgeHammer/Hammer repository

## Repository Structure

```text
sledgehammer-cloud/
├── docker/
│   └── Dockerfile
├── .dockerignore
├── .gitignore
└── README.md
```

## Build

Clone this repository and build the worker image:

```bash
git clone https://github.com/desvaun/sledgehammer-cloud.git
cd sledgehammer-cloud
docker build -f docker/Dockerfile -t sledgehammer-pd-worker:dev .
```

The Docker build retrieves SledgeHammer from the Hammer repository and checks out the `sledgehammer_merged` branch.

## Run

Start an interactive worker container:

```bash
docker run --rm -it sledgehammer-pd-worker:dev
```

## Verification

Check the installed Python version:

```bash
docker run --rm sledgehammer-pd-worker:dev python --version
```

Check Hammer:

```bash
docker run --rm sledgehammer-pd-worker:dev hammer-vlsi --help
```

Check SledgeHammer:

```bash
docker run --rm sledgehammer-pd-worker:dev sledgehammer --help
```

Check Airflow:

```bash
docker run --rm sledgehammer-pd-worker:dev airflow version
```

## Configuration

The base worker image is built with:

```text
SLEDGE_NO_PLUGINS=1
```

Technology plugins and deployment-specific resources are configured separately from the base image.

PostgreSQL credentials are not created during the Docker image build. Database configuration is supplied at deployment time.

## Notes

PDKs, EDA licenses, database credentials, private keys, and other sensitive or proprietary resources should not be committed to this repository.
