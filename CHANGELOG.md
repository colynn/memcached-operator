# ChangeLog

## webhook

> tag: [v0.0.3](https://github.com/colynn/memcached-operator/releases/tag/v0.0.3)

```sh
# Create Validation Webhook
operator-sdk create webhook --group cache --version v1alpha1 --kind Memcached --defaulting --programmatic-validation

# Generate webhook manifests and enable webhook deployment
make manifests
```

### reference

1. [Admission Webhooks](https://sdk.operatorframework.io/docs/building-operators/golang/webhook/)

## frist real world

> tag: [v0.0.2](https://github.com/colynn/memcached-operator/releases/tag/v0.0.2)

make memcached deployment running

1. 修改`api/v1alpha1/memcached_types.go`
    - `MemcachedSpec` 由`Foot` 无意义字段调整为有实际意义的`Size`字段
    - `MemcachedStatus` 添加`Nodes`字段用于记录the names of the memcached pods
    - 还有一些 CRD validation markers(rbac等相关的)
2. 因为我们修改了`memcached_types.go`,所以我们需要运行如下命令更新为资源类型生成的代码:

    ```sh
    make generate
    ```

3. Generating CRD manifests (Once the API is defined with spec/status fields and CRD validation markers)，需要运行如下命令生成和更新CRD Manifests:

    ```sh
    make manifests
    ```

4. 添加基础的`Reconcile`逻辑 (`controllers/memcached_controller.go`)
    - 检查cr资源
    - 创建deployment
    - 更新Memcached status.Nodes信息

5. 通过如下命令生成docker image(通过IMG参数自定义image的地址)

    ```sh
    make docker-build IMG=colynn/memcached-operator:latest
    ```

6. 修改memcached-operator manager的镜像版本信息并部署

    ```yaml
    # config/manager/kustomization.yaml
    ```

    ```sh
    make deploy
    ```

7. 定义Mecahced CR资源，修改`samples/cache_v1alpha1_memcached.yaml`
    - 添加`size`定义

```sh
    kubectl apply -f config/sample/cache_v1alpha1_mecached.yaml
```

__Finally__, 你将会在对应的kubernetes环境中看到如下的运行状态:

```sh
# memcached-operator manager
$ kubectl -n memcached-operator-system get pods
NAME                                                     READY   STATUS    RESTARTS   AGE
memcached-operator-controller-manager-79c444fb96-mbfgd   2/2     Running   0          38m

# memcached cr
$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
memcached-sample-6ccc5bb5db-2sfss   1/1     Running   0          60m
memcached-sample-6ccc5bb5db-6x5kv   1/1     Running   0          60m
memcached-sample-6ccc5bb5db-p9tjw   1/1     Running   0          60m
```

```yaml
# $ kubectl get memcacheds.cache.colynn.com memcached-sample -o yaml
apiVersion: cache.colynn.com/v1alpha1
kind: Memcached
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"cache.colynn.com/v1alpha1","kind":"Memcached","metadata":{"annotations":{},"name":"memcached-sample","namespace":"default"},"spec":{"size":3}}
  creationTimestamp: "2022-10-20T07:51:55Z"
  generation: 1
  managedFields:
  - apiVersion: cache.colynn.com/v1alpha1
    fieldsType: FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .: {}
          f:kubectl.kubernetes.io/last-applied-configuration: {}
      f:spec:
        .: {}
        f:size: {}
    manager: kubectl
    operation: Update
    time: "2022-10-20T07:51:55Z"
  - apiVersion: cache.colynn.com/v1alpha1
    fieldsType: FieldsV1
    fieldsV1:
      f:status:
        .: {}
        f:nodes: {}
    manager: manager
    operation: Update
    time: "2022-10-20T08:12:28Z"
  name: memcached-sample
  namespace: default
  resourceVersion: "98873361"
  selfLink: /apis/cache.colynn.com/v1alpha1/namespaces/default/memcacheds/memcached-sample
  uid: d724d918-c067-4096-a3f8-ef8d6abea11e
spec:
  size: 3
status:
  nodes:
  - memcached-sample-6ccc5bb5db-p9tjw
  - memcached-sample-6ccc5bb5db-6x5kv
  - memcached-sample-6ccc5bb5db-2sfss

```

__Cleanup__, 如果你想清理所有部署的资源，请运行如下命令:

```sh
kubectl delete -f config/samples/cache_v1alpha1_memcached.yaml
make undeploy
```

## init commit

> tag: [v0.0.1](https://github.com/colynn/memcached-operator/releases/tag/v0.0.1)

```sh
# iniitialize the project
operator-sdk init --domain colynn.com --repo github.com/colynn/memcached-operator

# create a simaple Memcached API
operator-sdk create api --group cache --version v1alpha1 --kind Memcached --resource --controller

# Next: implement your new API and generate the manifests (e.g. CRDs,CRs config/crd/bases/  config/rbac/role.yaml) with:
make manifests
```
