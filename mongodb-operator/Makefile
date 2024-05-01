.DEFAULT_GOAL := generate

VERSION=""
HELM_NAME="mongodb-operator"
HELM_CHART="mongodb/community-operator"

validate-version:
	@if [ "$(VERSION)" = "" ]; then echo "no version specified. (make VERSION=x.x.x)"; exit 1; fi

helm-add:
	@helm repo add mongodb https://mongodb.github.io/helm-charts >/dev/null 2>&1

helm-update:
	@helm repo update >/dev/null 2>&1

mkdir:
	@if [ -d "./$(VERSION)" ]; then echo "$(VERSION) already exists"; exit 1 ; else mkdir $(VERSION); mkdir $(VERSION)/crds/; fi

$(VERSION)/kustomization.yaml: mkdir
	@echo 'apiVersion: kustomize.config.k8s.io/v1beta1'	 > $(VERSION)/kustomization.yaml
	@echo 'kind: Kustomization'							>> $(VERSION)/kustomization.yaml
	@echo 												>> $(VERSION)/kustomization.yaml
	@echo 'resources:'									>> $(VERSION)/kustomization.yaml
	@echo '- crds/mongodb_community.yaml'				>> $(VERSION)/kustomization.yaml
	@echo '- database_roles.yaml'						>> $(VERSION)/kustomization.yaml
	@echo '- operator.yaml'								>> $(VERSION)/kustomization.yaml
	@echo '- operator_roles.yaml'						>> $(VERSION)/kustomization.yaml

generate: validate-version helm-add helm-update $(VERSION)/kustomization.yaml
	@echo "generating mongodb-operator $(VERSION)"
	@rm -rf ./tmp
	@helm template $(HELM_NAME) --version $(VERSION) --output-dir ./tmp --values ./values.yaml -n default $(HELM_CHART) >/dev/null
	@mv tmp/community-operator/templates/* $(VERSION)/.
	@mv tmp/community-operator/charts/community-operator-crds/templates/mongodbcommunity.mongodb.com_mongodbcommunity.yaml $(VERSION)/crds/mongodb_community.yaml
	@rm -rf ./tmp
	@echo "done"
