# ChangeLog

## init commit

> tag: v0.0.1

```sh
# iniitialize the project
operator-sdk init --domain colynn.com --repo github.com/colynn/memcached-operator

# create a simaple Memcached API
operator-sdk create api --group cache --version v1alpha1 --kind Memcached --resource --controller

# Next: implement your new API and generate the manifests (e.g. CRDs,CRs config/crd/bases/  config/rbac/role.yaml) with:
make manifests
```
