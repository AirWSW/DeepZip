#!/usr/bin/make

SHELL = /bin/sh

USER := $(shell whoami)
UID := $(shell id -u)

export USER
export UID

DATA?="${HOME}/Data"
GPU?=0
DOCKER_FILE=Dockerfile
# If the docker version >= 19.03, nvidia docker is native supported. Just use flags as follows to enable acceleration.
# DOCKER=docker run -it --rm --gpus all
# If the docker version < 19.03, nvidia-docker is required to enable acceleration.
DOCKER=GPU=$(GPU) nvidia-docker run -it --rm
BACKEND=tensorflow
PYTHON_VERSION?=3.6
CUDA_VERSION?=9.0
CUDNN_VERSION?=7
TEST=tests/
SRC?=$(shell dirname `pwd`)

help:
	@cat Makefile

build:
	docker build -t $(USER)/keras --build-arg nb_user=$(USER) --build-arg nb_uid=$(UID) --build-arg python_version=$(PYTHON_VERSION) --build-arg cuda_version=$(CUDA_VERSION) --build-arg cudnn_version=$(CUDNN_VERSION) -f $(DOCKER_FILE) .

bash: build
	$(DOCKER) -v $(SRC):/src/workspace -v $(DATA):/data --env KERAS_BACKEND=$(BACKEND) $(USER)/keras bash

ipython: build
	$(DOCKER) -v $(SRC):/src/workspace -v $(DATA):/data --env KERAS_BACKEND=$(BACKEND) $(USER)/keras ipython

notebook: build
	$(DOCKER) -v $(SRC):/src/workspace -v $(DATA):/data --net=host --env KERAS_BACKEND=$(BACKEND) $(USER)/keras

test: build
	$(DOCKER) -v $(SRC):/src/workspace -v $(DATA):/data --env KERAS_BACKEND=$(BACKEND) $(USER)/keras py.test $(TEST)

