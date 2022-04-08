# GitOps-Tutorial

## Note

I took most of what I know about GitOps from Christian Hernandez and his [youtube video](https://www.youtube.com/watch?v=o_lcRoiFtUU) GitOps from Scratch. Thank you Christian for how amazing it is.


## Prereqs

- OpenShift cluster
- Logged in with administrative privs
- Verify that there isn't any gitops pods
```bash
oc get pods -A | grep -i gitops
```
- The following is for a one to one (one cluster to one repo) design

## Steps

### bootstrapping 

- create a new repo (github public repo)
- create a bootstraps directories
```bash
mkdir <clustername>/bootstrap
mkdir <clustername>/bootstrap/base
mkdir <clustername>/bootstrap/overlays
```
-  create the file to install the gitops operator (openshift-gitops-sub.yaml)
- create a directory for kustomize (overlays/default) Note: this is where the deployment starts, it reads from base.
- create the kustomization file for base (base/kustomization.yaml)
- create the argocd installation for the overlay (overlays/default/openshift-gitops-argocd.yaml)
- create a sane default RBACs (overlays/default/openshift-gitops-rbac-policy.yaml)
- create a kustomization file for overlays (overlays/default/kustomization.yaml)
- run kustomize to build this out (this is the base build)
```bash
kustomize build os/bootstrap/overlays/default
```

### Overlay

- create a directory called components, add the configuration directory, and an argocdproj for different argocd projects
```bash
mkdir components/
mkdir components/applicationsets
mkdir components/argocdproj
```
- create a test project for argocd (components/argocdproj/test-project.yaml)
- create a kustomization file for the test-project (components/argocdproj/kustomization.yaml)
- update the overlay kustomization file to hook up the directories (bootstrap/overlays/default/kustomization.yaml)
- when you run the build the argocd project will be part of the bulid
```bash
kustomize build os/bootstrap/overlays/default
```
- create the core directory (os/core)
```bash
mkdir os/core
mkdir os/core/gitops-controller
```
- create a kustomization file in the gitops-controller directory (os/core/gitops-controller/kustomization.yaml)
- test the configuration by running build
```bash
kustomize build core/gitops-controller
```
- create the core application set (components/applicationsets/core-components-appset.yaml), you'll need to change line 12, and line 9 and 29 to the correct cluster and github repo.
- create a kustomization file in the application set directory (components/applicationsets/kustomization.yaml)
-  add the overlay to find the applicationsets (bootstrap/overlays/default/kustomization.yaml)
- test the configuration by running build
```bash
kustomize build os/core/gitops-controller/
```
- create a tennants (or app, this is your workloads) top directory (core is the appartment complex, while tenants is the well tenants)
```bash
mkdir os/tenants
```
- create an app into tenants TODO
- create another appset to hook the tenant into the system (components/applicationsets/tenants-appset.yaml)
- add the tenants-appset to the kustomization (components/applicationsets/kustomization.yaml)
- test the configuration by running build
```bash
kustomize build os/core/gitops-controller/
```

## License & Authors

If you would like to see the detailed LICENSE click [here](./LICENSE).


- Author: JJ Asghar <awesome@ibm.com>

```text
Copyright:: 2020- IBM, Inc

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```