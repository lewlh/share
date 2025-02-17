# Ollama

此 ollama-chart 用于在 kubernetes 部署 ollama。

## 安装

在本地直接使用命令调用

```bash
helm upgrade --install myOllama ollama
```

### 如何修改默认 Values 值

使用 --set 指定每个参数的值覆盖掉默认值。例如:

```bash
helm install [NAME] [CHART] --set key=value[,key=value]
```

或者，可以在安装 chart 时使用本地的 YAML 参数文件。例如：

```bash
helm install [NAME] -f my-values.yaml [CHART]
```

## 卸载

根据相应的 NAME 进行卸载

```bash
helm uninstall myOllama
```

## 更新 chart

使用 upgrade + --set 修改参数

```bash
helm upgrade --install myOllama ollama --set key=value[,key=value]
```

或者使用 upgrade + -f 指定 yaml 文件修改参数

```bash
helm upgrade --install myOllama -f my-values.yaml ollama
```

## 准备工作

+ Kubernetes 1.18+
+ Helm 3.3.1+

## 参数

### 常用参数

| name               | Description | value |
| --------------- | ------------------------------------------------------------------------------- | -- |
| config.ollamaHomePath | Ollama 模型文件存储路径 | `/mnt/cephfs/share/public/ollama` |
| nodePort.enabled   | 网络接口是否使用 NodePort（当为 true 时，网络接口使用 NodePort，否则为 ClusterIP） | false |
| nodePort.port      | 当使用 NodePort，外部服务可以直接通过 k8s集群IP:port 端口访问服务   | 30434 |
| nodeSelector       | nodeSelector指定键值对的映射 | `gpu: GTX1080Ti` |
| imagePullSecrets   | 镜像拉取认证密钥 | [ ] |
| nameOverride       | nameOverride替换Chart.yaml文件中图表的名称 | ""  |
| fullnameOverride   | fullnameOverride完全替换生成的名称         | ""  |
| podAnnotations     | pod 注释  | { }   |
| securityContext    | 定义 Container 的特权与访问控制设置 | { } |
| tolerations        | 允许（但并不要求）Pod 调度到带有与之匹配的污点的节点上。 | [ ] |
| affinity           | 节点亲和性| { } |

### ollama 容器镜像参数

| name             | Description   |                 value                  |
| ------------- | ------------------------------------------- | :---------: |
| image.repository | ollama 使用的 triton server 镜像文件存储库 | ollama/ollama |
| image.pullPolicy | 镜像拉取策略  | IfNotPresent |
| image.tag        | 镜像标签      | 0.5.11 |

### ollama 资源参数

| name   | Description| value |
| ---------------------- | -------------------------- | :---: |
| resources.requests.cpu    | ollama 容器请求的 cpu    | 2 |
| resources.requests.memory | ollama 容器请求的内存     | 16Gi |
| resources.requests.nvidia.com/gpu | ollama 容器请求的英伟达的GPU | 1 |
| resources.limits.cpu      | ollama 容器的cpu资源限制  | 10 |
| resources.limits.memory   | ollama 容器的内存资源限制 | 24Gi |
| resources.limits.nvidia.com/gpu | ollama 容器的英伟达的GPU资源限制 | 1 |

### ollama 弹性伸缩参数

| name      | Description       | value |
| --------------------------------------- | -------------- | :---: |
| autoscaling.enabled     | 是否启用 pod 水平自动扩展| false |
| autoscaling.minReplicas | 扩展时 pod 最少在线数量  |   1   |
| autoscaling.maxReplicas | 扩展时 pod 最多在线数量  |   2   |
| autoscaling.targetCPUUtilizationPercentage | 当 CPU 使用率持续超过 resources.requests.cpu 值的百分比，则会触发 autoscaling，增加新的服务副本数 | 400  |
| autoscaling.targetMemoryUtilizationPercentage | 当内存使用率持续超过 resources.requests.memory 值的百分比，则会触发 autoscaling，增加新的服务副本数 | 150  |

### ollama 代理服务参数

| name         | Description      |value |
| ---------------------------- | ---------------| :------------: |
| proxy.image.repository          | 代理服务使用的 envoy proxy 镜像文件存储库   | envoyproxy/envoy |
| proxy.image.pullPolicy          | 代理服务镜像拉取策略    | IfNotPresent |
| proxy.image.tag                 | 代理服务镜像标签 | v1.33.0 |
| proxy.numRetries                | 重试次数        |  5   |
| proxy.maxRequestsPerConnection  | 每个HTTP2连接最大的请求数 |  8   |
| proxy.lb.choiceCount            | 负载均衡最少请求数策略随机选择多少个server计算最小请求数 |  5   |
| proxy.resources.requests.cpu    | 代理服务容器请求的 cpu  | 50m  |
| proxy.resources.requests.memory | 代理服务容器请求的 内存 | 64Mi |
| proxy.resources.limits.cpu      | 代理服务容器的cpu资源限制 | 500m |
| proxy.resources.limits.memory   | 代理服务容器的内存资源限制 |640Mi |

## ChangeLog
