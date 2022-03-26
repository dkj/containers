TAG=$(shell git describe --always)
LABEL_NAMESPACE=uk.ac.sanger
ifdef GITHUB_ACTIONS
DOCKER_REGISTRY?=ghcr.io
DOCKER_USER?=$(GITHUB_ACTOR)
TAG_PREFIX?=$(DOCKER_REGISTRY)/$(DOCKER_USER)/
else
DOCKER_REGISTRY=docker.io
DOCKER_USER?=wsinpg
TAG_PREFIX?=$(DOCKER_USER)/
endif

DOCKER_ARGS ?= --platform linux/amd64

.PHONY: clean

image_names := ub-16.04-base ub-18.04-base
image_names += ub-16.04-irods-4.2.7
image_names += ub-18.04-irods-4.2.10
image_names += ub-18.04-irods-4.2.11

image_names += centos-7-base
image_names += centos-7-conda
image_names += centos-7-conda-build

image_names += md-bullseye-base
image_names += md-bullseye-conda

git_url=$(shell git remote get-url origin)
git_commit=$(shell git log --pretty=format:'%H' -n 1)

images := $(addsuffix .$(TAG), $(image_names))
remote := $(addsuffix .$(TAG).pushed, $(image_names))

all: $(images)

push: $(remote)

centos-7-base.$(TAG): base/centos/7/Dockerfile
	docker build $(DOCKER_ARGS) \
	--label $(LABEL_NAMESPACE).repository=$(git_url) \
	--label $(LABEL_NAMESPACE).commit=$(git_commit) \
	--tag $(TAG_PREFIX)centos-7-base:latest \
	--tag $(TAG_PREFIX)centos-7-base:$(TAG) --file $< ./base
	touch $@

ub-16.04-base.$(TAG): base/ubuntu/16.04/Dockerfile
	docker build $(DOCKER_ARGS) \
	--label $(LABEL_NAMESPACE).repository=$(git_url) \
	--label $(LABEL_NAMESPACE).commit=$(git_commit) \
	--tag $(TAG_PREFIX)ub-16.04-base:latest \
	--tag $(TAG_PREFIX)ub-16.04-base:$(TAG) --file $< ./base
	touch $@

ub-18.04-base.$(TAG): base/ubuntu/18.04/Dockerfile
	docker build $(DOCKER_ARGS) \
	--label $(LABEL_NAMESPACE).repository=$(git_url) \
	--label $(LABEL_NAMESPACE).commit=$(git_commit) \
	--tag $(TAG_PREFIX)ub-18.04-base:latest \
	--tag $(TAG_PREFIX)ub-18.04-base:$(TAG) --file $< ./base
	touch $@

md-bullseye-base.$(TAG): base/minideb/bullseye/Dockerfile
	docker build $(DOCKER_ARGS) \
	--label $(LABEL_NAMESPACE).repository=$(git_url) \
	--label $(LABEL_NAMESPACE).commit=$(git_commit) \
	--tag $(TAG_PREFIX)md-bullseye-base:latest \
	--tag $(TAG_PREFIX)md-bullseye-base:$(TAG) --file $< ./base
	touch $@

centos-7-conda.$(TAG): conda/centos/7/Dockerfile centos-7-base.$(TAG)
	docker build $(DOCKER_ARGS) \
	--build-arg FROM_PREFIX=$(TAG_PREFIX) \
	--label $(LABEL_NAMESPACE).repository=$(git_url) \
	--label $(LABEL_NAMESPACE).commit=$(git_commit) \
	--tag $(TAG_PREFIX)centos-7-conda:latest \
	--tag $(TAG_PREFIX)centos-7-conda:$(TAG) --file $< ./conda
	touch $@

centos-7-conda-build.$(TAG): conda/centos/7/build/Dockerfile centos-7-conda.$(TAG)
	docker build $(DOCKER_ARGS) \
	--build-arg FROM_PREFIX=$(TAG_PREFIX) \
	--label $(LABEL_NAMESPACE).repository=$(git_url) \
	--label $(LABEL_NAMESPACE).commit=$(git_commit) \
	--tag $(TAG_PREFIX)centos-7-conda-build:latest \
	--tag $(TAG_PREFIX)centos-7-conda-build:$(TAG) --file $< ./conda
	touch $@

md-bullseye-conda.$(TAG): conda/minideb/bullseye/Dockerfile md-bullseye-base.$(TAG)
	docker build $(DOCKER_ARGS) \
	--build-arg FROM_PREFIX=$(TAG_PREFIX) \
	--label $(LABEL_NAMESPACE).repository=$(git_url) \
	--label $(LABEL_NAMESPACE).commit=$(git_commit) \
	--tag $(TAG_PREFIX)md-bullseye-conda:latest \
	--tag $(TAG_PREFIX)md-bullseye-conda:$(TAG) --file $< ./conda
	touch $@

ub-16.04-irods-4.2.7.$(TAG): irods/ubuntu/16.04/Dockerfile ub-16.04-base.$(TAG)
	docker build $(DOCKER_ARGS) \
	--build-arg FROM_PREFIX=$(TAG_PREFIX) \
	--label $(LABEL_NAMESPACE).repository=$(git_url) \
	--label $(LABEL_NAMESPACE).commit=$(git_commit) \
	--tag $(TAG_PREFIX)ub-16.04-irods-4.2.7:latest \
	--tag $(TAG_PREFIX)ub-16.04-irods-4.2.7:$(TAG) --file $< ./irods
	touch $@

ub-18.04-irods-4.2.10.$(TAG): irods/ubuntu/18.04/Dockerfile ub-18.04-base.$(TAG)
	docker build $(DOCKER_ARGS) --build-arg IRODS_VERSION=4.2.10 \
	--build-arg FROM_PREFIX=$(TAG_PREFIX) \
	--label $(LABEL_NAMESPACE).repository=$(git_url) \
	--label $(LABEL_NAMESPACE).commit=$(git_commit) \
	--tag $(TAG_PREFIX)ub-18.04-irods-4.2.10:latest \
	--tag $(TAG_PREFIX)ub-18.04-irods-4.2.10:$(TAG) --file $< ./irods
	touch $@

ub-18.04-irods-4.2.11.$(TAG): irods/ubuntu/18.04/Dockerfile ub-18.04-base.$(TAG)
	docker build $(DOCKER_ARGS) --build-arg IRODS_VERSION=4.2.11-1~bionic \
	--build-arg FROM_PREFIX=$(TAG_PREFIX) \
	--label $(LABEL_NAMESPACE).repository=$(git_url) \
	--label $(LABEL_NAMESPACE).commit=$(git_commit) \
	--tag $(TAG_PREFIX)ub-18.04-irods-4.2.11:latest \
	--tag $(TAG_PREFIX)ub-18.04-irods-4.2.11:$(TAG) --file $< ./irods
	touch $@

%.$(TAG).pushed: %.$(TAG)
	docker push $(TAG_PREFIX)$*:$(TAG)
	touch $@

remote-clean:
	rm -f $(remote)

clean:
	rm -f $(foreach image_name,$(image_names), $(image_name)*)
