.PHONY: all
.PHONY: docker-build-all docker-push-all

VERSION?=23.03
# VERSION can be 23.03-light
REGISTRY?=xfan1024/openeuler
TAG?=$(VERSION)

all: docker-build-all

define docker-build

.PHONY: docker-build-$(1)
docker-build-list += docker-build-$(1)
docker-build-$(1):
	docker buildx build --platform linux/$(1) image \
		--build-arg BASEFILE=$$$$(sh makehelper basefile $(1)) \
		--build-arg DELETEREPO=$(2)  \
		--build-arg RPMARCH=$$$$(sh makehelper rpmarch $(1)) \
		-f image/Dockerfile.$(VERSION) \
		-t $(REGISTRY):$(TAG)-$(1)

.PHONY: docker-push-$(1)
docker-push-list += docker-push-$(1)
docker-push-$(1):
	docker push $(REGISTRY):$(TAG)-$(1)
	docker manifest create --amend $(REGISTRY):$(TAG) $(REGISTRY):$(TAG)-$(1)

endef

$(eval $(call docker-build,amd64,openEuler-temp.repo))
$(eval $(call docker-build,arm64,openEuler-temp.repo))
$(eval $(call docker-build,riscv64,openEuler.repo))

docker-build-all: $(docker-build-list)
docker-push-all: $(docker-push-list)
	docker manifest push --purge $(REGISTRY):$(TAG)
