## Here's how to set up a new area (mimir/GEM for example), similar to this one for tempo/GET.

## 1) Changes in this repo

New subfolders:
```
tempo-distributed
- base
- ns-only
- overlays
  - GEM
```

Get the current chart version number (example as of this writing: 1.23.2)
```
helm repo update
helm search repo | grep grafana/tempo-distributed
```

Pull down the full values file from the Helm chart:
```
helm show values grafana/tempo-distributed > ./base/values.yaml
```

> (there is a TODO at this point: should a custom values file provided by Grafana also be stored here? Or should that all happen through Kustomize?)

build the templates from the helm chart
```
helm template tempo grafana/tempo-distributed \
  --version 1.23.2 \
  --namespace tempo \
  --no-hooks \
  --values ./base/values.yaml \
  --output-dir ./base
```

that builds the templates into this path:
./base/tempo-distributed/templates/

> (another TODO: decide whether to keep them in that structure, or simplify it)

Copy and hold onto the output of that command (the printed list of all the files that were written).

In base, create a new kustomization manifest; see the example in ./base.

Paste the printed list in the resources section, then use global find/replace (don't include these quotes, they are here to show spaces):
```
find:    "wrote ./base/"
replace: "  - "
```

In base, create a manifest file for the default namespace to use.

In the "ns-only" folder, create a kustomization manifest; see the example for this project.

## 2) Changes in the Flux repo

The Flux repo also has to be set up to point to this one.
In the [example here](https://github.com/danstadler-pdx/fleet-infra-grafana-gke/blob/main/clusters/cluster-one/grafana/tempo-distributed/tempo-kustomization.yaml),
you can see how Flux can be initialized to just create the namespace, but not run a full base or overlay deployment. This is 
handy for showing an indication that the Flux setup is ready to deploy an app, but hasn't actually been told to do so.









