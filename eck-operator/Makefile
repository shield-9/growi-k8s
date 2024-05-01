.DEFAULT_GOAL := generate

VERSION=""
HELM_NAME="eck-operator"
HELM_CHART="elastic/eck-operator"

validate-version:
	@if [ "$(VERSION)" = "" ]; then echo "no version specified. (make VERSION=x.x.x)"; exit 1; fi

helm-add:
	@helm repo add elastic https://helm.elastic.co >/dev/null 2>&1

helm-update:
	@helm repo update >/dev/null 2>&1

mkdir:
	@if [ -d "./$(VERSION)" ]; then echo "$(VERSION) already exists"; exit 1 ; else mkdir $(VERSION); mkdir $(VERSION)/crds; fi

$(VERSION)/kustomization.yaml: mkdir
	@echo 'apiVersion: kustomize.config.k8s.io/v1beta1'	 > $(VERSION)/kustomization.yaml
	@echo 'kind: Kustomization'							>> $(VERSION)/kustomization.yaml
	@echo 												>> $(VERSION)/kustomization.yaml
	@echo 'resources:'									>> $(VERSION)/kustomization.yaml
	@echo '- cluster-roles.yaml'						>> $(VERSION)/kustomization.yaml
	@echo '- configmap.yaml'							>> $(VERSION)/kustomization.yaml
	@echo '- crds/all-crds.yaml'						>> $(VERSION)/kustomization.yaml
	@echo '- role-bindings.yaml'						>> $(VERSION)/kustomization.yaml
	@echo '- service-account.yaml'						>> $(VERSION)/kustomization.yaml
	@echo '- statefulset.yaml'							>> $(VERSION)/kustomization.yaml
	@echo '- webhook.yaml'								>> $(VERSION)/kustomization.yaml


generate: validate-version helm-add helm-update $(VERSION)/kustomization.yaml
	@echo "generating eck-operator $(VERSION)"
	@rm -rf ./tmp
	@helm template $(HELM_NAME) --version $(VERSION) --output-dir ./tmp --values ./values.yaml -n default $(HELM_CHART) >/dev/null
	@mv tmp/eck-operator/templates/* $(VERSION)/.
	@mv tmp/eck-operator/charts/eck-operator-crds/templates/* $(VERSION)/crds/.
	@rm -rf ./tmp
	@echo "done"
