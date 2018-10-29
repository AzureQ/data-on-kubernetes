# Availability: Affinity, Anti-Affinity, PodDisruptionBudget

[Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity) in Kubernetes provides a great way to specify any deployment topology users preferred, which can help provide better availability of Kafka and Zookeeper Service on Kubernetes by assigning Kafka or Zookeeper Pods to dedicated nodes, distributing Pods across nodes or even Availability Zones, etc. This feature includes:

* Node affinity 
  * requiredDuringSchedulingIgnoredDuringExecution \("Hard"\)
  * preferredDuringSchedulingIgnoredDuringExecution \("Soft"\)
* Inter-pod affinity/anti-affinity
  * requiredDuringSchedulingIgnoredDuringExecution \("Hard"\)
  * preferredDuringSchedulingIgnoredDuringExecution \("Soft"\)

These features are perfect to enforcing certain deployment topologies of Kafka and Zookeeper cluster such as Pods should be across nodes, or maybe even across Availability Zones, etc. For example, we want to make sure that a 3 Pods Zookeeper cluster has Pods deployed across different worker nodes but also want to make it runnable on Minikube, we can define a "soft" `podAntiAffinity` rule as below:

```text
apiVersion: apps/v1beta1
kind: StatefulSet
metadata:
  name: <StatefulSet Name>
spec:
  serviceName: <Headless Service Name>
  replicas: <Number of Pods>
  template:
    metadata:
      labels:
        <labels>
    spec:
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 1
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - {{ template "cp-zookeeper.name" . }}
                  - key: "release"
                    operator: In
                    values:
                    - {{ .Release.Name }}
              topologyKey: "kubernetes.io/hostname"
      containers:
        <Containers Spec>
  volumeClaimTemplates:
    <Persistent Volume Claims>
```

[PodDisruptionBudget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) is another important feature to help with availability from a different angel, it limits the number pods of a replicated application that are down simultaneously from voluntary disruptions.For example, a quorum-based application such as Zookeeper would like to ensure that the number of replicas running is never brought below the number needed for a quorum.

**Note**: 

1. A PDB is only accounted for with a [voluntary disruption](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/#voluntary-and-involuntary-disruptions), something like a hardware failure will not take PDB into account.
2. You can specify **only one** of `maxUnavailable` and `minAvailable` in a single PodDisruptionBudget

```text
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
  name: <PodDisruptionBudget Name>
  labels:
    <labels>
spec:
  selector:
    matchLabels:
      <match labels>
  minAvailable: <number or percentage>
  maxUnavailable: <number or percentage>
```

