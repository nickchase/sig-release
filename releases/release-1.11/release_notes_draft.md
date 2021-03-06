# Kubernetes 1.11 Release Notes

This document is for release notes for Kubernetes 1.11. These release notes are initially generated from commit messages, and are sorted based on the release note tag.

Commits should be tagged as follows:

**/release-note-before-upgrading**

This tag is for urgent changes that users must take into consideration before upgrading. For example, mandatory upgrades of dependencies or other upgrade/downgrade issues belong in this category.

**/release-note-feature**

This tag is for changes that are related to major features that are listed in the feature tracking spreadsheet.

**/release-note**

This tag is for changes that are user-facing. For example, API changes or CLI flag changes belong in this category. Fixed bugs that affect the user experience should also be in this category.

**/release-note-none**

This tag is for changes that don't affect the user experience, such as behind-the-scenes improvements that only other Kubernetes developers will notice. These notes will appear in the changelog but not the release notes.

## Draft Log

- April 24, 2018: Added pending notes from v1.10.0 to to 3dbcd1d ([@marpaia](https://github.com/marpaia))
- April 28, 2018: Sorted through 4/24/18 notes ([@nickchase](https://github.com/nickchase))

## Before Upgrading

* Default mount propagation has changed from "HostToContainer" ("rslave" in Linux terminology), as it was in 1.10, to "None" ("private") to match the behavior in 1.9 and earlier releases. "HostToContainer" as a default caused regressions in some pods. ([#62462](https://github.com/kubernetes/kubernetes/pull/62462), [@jsafrane](https://github.com/jsafrane))
* The kube-apiserver `--storage-version` flag has been removed; you must use `--storage-versions` instead. ([#61453](https://github.com/kubernetes/kubernetes/pull/61453), [@hzxuzhonghu](https://github.com/hzxuzhonghu))


### Pending

* client-go developers must switch to the new dynamic client. This new client is easier to use, and the old one is deprecated and will be removed in a future version. **(EDITOR'S NOTE:  If the old client is deprecated, it's still there, so do developers actually HAVE to switch, and if so, why?)** (#62913, @deads2k)
* Add warnings that authors of aggregated API servers must not rely on authorization being done by the kube-apiserver. **(EDITOR'S NOTE: Why? And is this note that warnings must be added, or is this note the warning?)** ([#61349](https://github.com/kubernetes/kubernetes/pull/61349), [@sttts](https://github.com/sttts))

#### New Deprecations

* `--show-all`, which only affected pods, and even then only for human readable/non-API printers, is inert in v1.11, and will be removed in a future release. ([#60793](https://github.com/kubernetes/kubernetes/pull/60793), [@charrywanganthony](https://github.com/charrywanganthony))
* Deprecate kubectl rolling-update  (#61285, @soltysh)

#### Removed Deprecations

* --include-extended-apis, which was deprecated back in [#32894](https://github.com/kubernetes/kubernetes/pull/32894), has been removed. ([#62803](https://github.com/kubernetes/kubernetes/pull/62803), [@deads2k](https://github.com/deads2k))
* Kubelets will no longer set `externalID` in their node spec. ([#61877](https://github.com/kubernetes/kubernetes/pull/61877), [@mikedanese](https://github.com/mikedanese))
* The initresource admission plugin has been removed. ([#58784](https://github.com/kubernetes/kubernetes/pull/58784), [@wackxu](https://github.com/wackxu))
* `ObjectMeta `, `ListOptions`, and `DeleteOptions` have been removed from the core api group.  Please reference them in `meta/v1`. ([#61809](https://github.com/kubernetes/kubernetes/pull/61809), [@hzxuzhonghu](https://github.com/hzxuzhonghu))
* The deprecated --mode flag in check-network-mode has been removed. ([#60102](https://github.com/kubernetes/kubernetes/pull/60102), [@satyasm](https://github.com/satyasm))
* Support for the `alpha.kubernetes.io/nvidia-gpu` resource, which was deprecated in 1.10, has been removed. Please use the resource exposed by DevicePlugins instead (`nvidia.com/gpu`). ([#61498](https://github.com/kubernetes/kubernetes/pull/61498), [@mindprince](https://github.com/mindprince))
* The kube-cloud-controller-manager flag --service-account-private-key-file has been removed. ([#60875](https://github.com/kubernetes/kubernetes/pull/60875), [@charrywanganthony](https://github.com/charrywanganthony))

**Pending**
* The rknetes code, which was deprecated in 1.10, has been removed. **(What should be used instead? CRI?)** ([#61432](https://github.com/kubernetes/kubernetes/pull/61432), [@filbranden](https://github.com/filbranden))
* DaemonSet scheduling associated with the alpha ScheduleDaemonSetPods feature flag has been removed from the 1.10 release. See https://github.com/kubernetes/features/issues/548 for feature status. **(EDITOR'S NOTE: Should this be 1.11 or is this a leftover change that didn't quite make it?)** ([#61411](https://github.com/kubernetes/kubernetes/pull/61411), [@liggitt](https://github.com/liggitt))
* The METADATA_AGENT_VERSION configuration option has been removed. **(EDITOR'S NOTE: From where? What should be used instead?)** (#63000, @kawych)

#### Alpha, Beta, Stable

* The --experimental-qos-reserve kubelet flags has been replaced by the alpha level --qos-reserved flag or the QOSReserved field in the kubeletconfig, and requires the QOSReserved feature gate to be enabled. (#62509, @sjenning)

**Pending**

* Removed alpha functionality that allowed the controller manager to approve kubelet server certificates. **(EDITOR'S NOTE: Has this been replaced with something else? What is the effect on the user?)** ([#62471](https://github.com/kubernetes/kubernetes/pull/62471), [@mikedanese](https://github.com/mikedanese))

## Other Notable Changes

### Sig API-Machinery
* The kubeadm config option `API.ControlPlaneEndpoint` has been extended to take an optional port which may differ from the apiserver's bind port. ([#62314](https://github.com/kubernetes/kubernetes/pull/62314), [@rjosephwright](https://github.com/rjosephwright))


### Sig Auth
* RBAC information is now included in audit logs via audit.Event annotations: ([#58807](https://github.com/kubernetes/kubernetes/pull/58807), [@CaoShuFeng](https://github.com/CaoShuFeng))
    * authorization.k8s.io/decision = {allow, forbid}
    * authorization.k8s.io/reason = human-readable reason for the decision
* `kubectl certificate approve|deny` will not modify an already approved or denied CSR unless the `--force` flag is provided. ([#61971](https://github.com/kubernetes/kubernetes/pull/61971), [@smarterclayton](https://github.com/smarterclayton))
* The `--bootstrap-kubeconfig` argument to Kubelet previously created the first bootstrap client credentials in the certificates directory as `kubelet-client.key` and `kubelet-client.crt`.  Subsequent certificates created by cert rotation were created in a combined PEM file that was atomically rotated as `kubelet-client-DATE.pem` in that directory, which meant clients relying on the `node.kubeconfig` generated by bootstrapping would never use a rotated cert.  The initial bootstrap certificate is now generated into the cert directory as a PEM file and symlinked to `kubelet-client-current.pem` so that the generated kubeconfig remains valid after rotation. (#62152, @smarterclayton)

### Sig Azure
* Azure cloud provider now supports standard SKU load balancer and public IP. To use it, set the cloud provider config with 
    {
       "loadBalancerSku": "standard",
       "excludeMasterFromStandardLB": true,
    }
If excludeMasterFromStandardLB is not set, it will be default to true, which means master nodes are excluded from the standard load balancer. Also note that the standard load balancer doesn't work with annotation `service.beta.kubernetes.io/azure-load-balancer-mode`. This is because all nodes (except master) are added as the loadbalancer backends.
([#61884](https://github.com/kubernetes/kubernetes/pull/61884), [#62707](https://github.com/kubernetes/kubernetes/pull/62707), [@feiskyer](https://github.com/feiskyer))
* The Azure cloud provider now supports specifying allowed service tags by annotation `service.beta.kubernetes.io/azure-allowed-service-tags` ([#61467](https://github.com/kubernetes/kubernetes/pull/61467), [@feiskyer](https://github.com/feiskyer))

### Sig Cluster-Lifecycle
* Phase `kubeadm alpha phase kubelet` has been added to support dynamic kubelet configuration in kubeadm. ([#57224](https://github.com/kubernetes/kubernetes/pull/57224), [@xiangpengzhao](https://github.com/xiangpengzhao))
* `kubeadm alpha phase kubeconfig user` now supports the specification of groups (organizations) in a client certificate. ([#62627](https://github.com/kubernetes/kubernetes/pull/62627), [@xiangpengzhao](https://github.com/xiangpengzhao))
* The --cluster-name parameter has been added to kubeadm init, enabling users to specify the cluster name in kubeconfig. ([#60852](https://github.com/kubernetes/kubernetes/pull/60852), [@karan](https://github.com/karan))
* The logging feature for kubeadm commands now supports a verbosity setting. ([#57661](https://github.com/kubernetes/kubernetes/pull/57661), [@vbmade2000](https://github.com/vbmade2000))
* kubeadm config can now override the Node CIDR Mask Size passed to kube-controller-manager. ([#61705](https://github.com/kubernetes/kubernetes/pull/61705), [@jstangroome](https://github.com/jstangroome))
* kubeadm now has a join timeout that can be controlled via the discoveryTimeout config option (set to 5 minutes by default). ([#60983](https://github.com/kubernetes/kubernetes/pull/60983), [@rosti](https://github.com/rosti))
* kubeadm: Added the writable boolean option to kubeadm config. The option works on a per-volume basis for *ExtraVolumes* config keys. ([#60428](https://github.com/kubernetes/kubernetes/pull/60428), [@rosti](https://github.com/rosti))
* Fixed a configuration error when upgrading kubeadm from 1.9 to 1.10+; Kubernetes must have the same major and minor versions as the kubeadm library. ([#62568](https://github.com/kubernetes/kubernetes/pull/62568), [@liztio](https://github.com/liztio))

### Sig GCP
* [fluentd-gcp addon] Increase CPU limit for fluentd to 1 core to achieve 100kb/s throughput. ([#62430](https://github.com/kubernetes/kubernetes/pull/62430), [@bmoyles0117](https://github.com/bmoyles0117))

### Sig Networking
* The internal IP address of the node is now added as additional information for kubectl, ([#57623](https://github.com/kubernetes/kubernetes/pull/57623), [@dixudx](https://github.com/dixudx))
* NetworkPolicies can now target specific pods in other namespaces by including both a namespaceSelector and a podSelector in the same peer element. ([#60452](https://github.com/kubernetes/kubernetes/pull/60452), [@danwinship](https://github.com/danwinship))
* kubelet's --cni-bin-dir option now accepts multiple comma-separated CNI binary directory paths, which are searched for CNI plugins in the given order. ([#58714](https://github.com/kubernetes/kubernetes/pull/58714), [@dcbw](https://github.com/dcbw))
* Added --ipvs-exclude-cidrs flag to kube-proxy.  ([#62083](https://github.com/kubernetes/kubernetes/pull/62083), [@rramkumar1](https://github.com/rramkumar1))

### Sig Scheduler
* kube-scheduler now has the --write-config-to flag ([#62515](https://github.com/kubernetes/kubernetes/pull/62515), [@resouer](https://github.com/resouer))
* Performance of the affinity/anti-affinity predicate for the default scheduler has been significantly improved. ([#62211](https://github.com/kubernetes/kubernetes/pull/62211), [@bsalamat](https://github.com/bsalamat))

### Sig Storage
* gitRepo volumes in pods no longer require git 1.8.5 or newer; older git versions are now supported. ([#62394](https://github.com/kubernetes/kubernetes/pull/62394), [@jsafrane](https://github.com/jsafrane))
* Added support for resizing Portworx volumes. (#62308, @harsh-px)

### Uncategorized

* Added prometheus cluster monitoring addon to kube-up ([#62195](https://github.com/kubernetes/kubernetes/pull/62195), [@serathius](https://github.com/serathius))
* kube-apiserver: oidc authentication now supports requiring specific claims with `--oidc-required-claim=<claim>=<value>` ([#62136](https://github.com/kubernetes/kubernetes/pull/62136), [@rithujohn191](https://github.com/rithujohn191))
* The CRD OpenAPI v3 specification for validation now allows additionalProperties, which are mutually exclusive to properties. ([#62333](https://github.com/kubernetes/kubernetes/pull/62333), [@sttts](https://github.com/sttts))
* Extend the Stackdriver Metadata Agent by adding a new Deployment for ingesting unscheduled pods and services.   ([#62043](https://github.com/kubernetes/kubernetes/pull/62043), [@supriyagarg](https://github.com/supriyagarg))
* Kubelet now exposes a new endpoint, `/metrics/probes`, which exposes a Prometheus metric containing the liveness and/or readiness probe results for a container. ([#61369](https://github.com/kubernetes/kubernetes/pull/61369), [@rramkumar1](https://github.com/rramkumar1))
* `kubectl patch` now supports `--dry-run`. ([#60675](https://github.com/kubernetes/kubernetes/pull/60675), [@timoreimann](https://github.com/timoreimann))
* You can now use the `base64decode` function in kubectl go templates to decode base64-encoded data such as `kubectl get secret SECRET -o go-template='{{ .data.KEY | base64decode }}'`. ([#60755](https://github.com/kubernetes/kubernetes/pull/60755), [@glb](https://github.com/glb))
* Added `MatchFields` to `NodeSelectorTerm`; in 1.11, it only supports `metadata.name`. ([#62002](https://github.com/kubernetes/kubernetes/pull/62002), [@k82cn](https://github.com/k82cn))

### Pending

* kubectl no longer renders a List as suffix kind name for CRD resources ([#62512](https://github.com/kubernetes/kubernetes/pull/62512), [@dixudx](https://github.com/dixudx))
* Updated kube-dns to Version 1.14.10. Major changes: ([#62676](https://github.com/kubernetes/kubernetes/pull/62676), [@MrHohn](https://github.com/MrHohn))
    * Fixed a bug in DNS resolution for externalName services
    * and PTR records that need to query from upstream nameserver.
* Add generators for `apps/v1` deployments. ([#61288](https://github.com/kubernetes/kubernetes/pull/61288), [@ayushpateria](https://github.com/ayushpateria))
* kubectl: improves compatibility with older servers when creating/updating API objects **(EDITOR'S NOTE: Specifically ...?) ([#61949](https://github.com/kubernetes/kubernetes/pull/61949), [@liggitt](https://github.com/liggitt))
* `kubectl apply` view/edit-last-applied support completion. ([#60499](https://github.com/kubernetes/kubernetes/pull/60499), [@superbrothers](https://github.com/superbrothers))
* Add all kinds of resource objects' statuses in HPA description. ([#59609](https://github.com/kubernetes/kubernetes/pull/59609), [@zhangxiaoyu-zidif](https://github.com/zhangxiaoyu-zidif))
* Add apiserver configuration option to choose audit output version. **(Which is?)** ([#60056](https://github.com/kubernetes/kubernetes/pull/60056), [@crassirostris](https://github.com/crassirostris))
* Implement preemption for extender with a verb and new interface ([#58717](https://github.com/kubernetes/kubernetes/pull/58717), [@resouer](https://github.com/resouer))

#### New Stuff


#### Version Upgrades

* Update version of Istio addon from 0.5.1 to 0.6.0. ([#61911](https://github.com/kubernetes/kubernetes/pull/61911), [@ostromart](https://github.com/ostromart))
    * See https://istio.io/about/notes/0.6.html for full Isto release notes.
* Upgrade the default etcd server version to 3.2.18 ([#61198](https://github.com/kubernetes/kubernetes/pull/61198), [@jpbetz](https://github.com/jpbetz))
* GCE: Bump GLBC version to 1.1.0 - supporting multiple certificates and HTTP2 ([#62427](https://github.com/kubernetes/kubernetes/pull/62427), [@nicksardo](https://github.com/nicksardo))
* Cluster Autoscaler 1.2.1 (release notes: https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.2.1) ([#62457](https://github.com/kubernetes/kubernetes/pull/62457), [@mwielgus](https://github.com/mwielgus))
* Update kube-dns to Version 1.14.9 in kubeadm. ([#61918](https://github.com/kubernetes/kubernetes/pull/61918), [@MrHohn](https://github.com/MrHohn))
* GCE: Updates GLBC version to 1.0.1 which includes a fix which prevents multi-cluster ingress objects from creating full load balancers. ([#62075](https://github.com/kubernetes/kubernetes/pull/62075), [@nicksardo](https://github.com/nicksardo))
* Update to use go1.10.1 ([#60597](https://github.com/kubernetes/kubernetes/pull/60597), [@cblecker](https://github.com/cblecker))
* Rev the Azure SDK for networking to 2017-06-01 ([#61955](https://github.com/kubernetes/kubernetes/pull/61955), [@brendandburns](https://github.com/brendandburns))
* Update kube-dns to Version 1.14.9. Major changes: ([#61908](https://github.com/kubernetes/kubernetes/pull/61908), [@MrHohn](https://github.com/MrHohn))
    * - Fix for kube-dns returns NXDOMAIN when not yet synced with apiserver.
    * - Don't generate empty record for externalName service.
    * - Add validation for upstreamNameserver port.
    * - Update go version to 1.9.3.
* Cluster Autoscaler 1.2.0 - release notes available here: https://github.com/kubernetes/autoscaler/releases ([#61561](https://github.com/kubernetes/kubernetes/pull/61561), [@mwielgus](https://github.com/mwielgus))
* Bump Heapster to v1.5.2 ([#61396](https://github.com/kubernetes/kubernetes/pull/61396), [@kawych](https://github.com/kawych))

#### Not Very Notable

* [fluentd-gcp addon] Update event-exporter image to have the latest base image. ([#61727](https://github.com/kubernetes/kubernetes/pull/61727), [@crassirostris](https://github.com/crassirostris))
* cluster/kube-up.sh now provisions a Kubelet config file for GCE via the metadata server. This file is installed by the corresponding GCE init scripts. ([#62183](https://github.com/kubernetes/kubernetes/pull/62183), [@mtaufen](https://github.com/mtaufen))
* Make volume usage metrics available for Cinder ([#62668](https://github.com/kubernetes/kubernetes/pull/62668), [@zetaab](https://github.com/zetaab))
* cinder volume plugin :  ([#61082](https://github.com/kubernetes/kubernetes/pull/61082), [@wenlxie](https://github.com/wenlxie))
    * When the cinder volume status is `error`,  controller will not do `attach ` and `detach ` operation
* Allow user to scale l7 default backend deployment ([#62685](https://github.com/kubernetes/kubernetes/pull/62685), [@freehan](https://github.com/freehan))
* Add support to ingest log entries to Stackdriver against new "k8s_container" and "k8s_node" resources. ([#62076](https://github.com/kubernetes/kubernetes/pull/62076), [@qingling128](https://github.com/qingling128))
* Disabled CheckNodeMemoryPressure and CheckNodeDiskPressure predicates if TaintNodesByCondition enabled ([#60398](https://github.com/kubernetes/kubernetes/pull/60398), [@k82cn](https://github.com/k82cn))
* Support custom test configuration for IPAM performance integration tests ([#61959](https://github.com/kubernetes/kubernetes/pull/61959), [@satyasm](https://github.com/satyasm))
* OIDC authentication now allows tokens without an "email_verified" claim when using the "email" claim. If an "email_verified" claim is present when using the "email" claim, it must be `true`. ([#61508](https://github.com/kubernetes/kubernetes/pull/61508), [@rithujohn191](https://github.com/rithujohn191))
* Add e2e test for CRD Watch ([#61025](https://github.com/kubernetes/kubernetes/pull/61025), [@ayushpateria](https://github.com/ayushpateria))
* Return error if get NodeStageSecret and NodePublishSecret failed in CSI volume plugin ([#61096](https://github.com/kubernetes/kubernetes/pull/61096), [@mlmhl](https://github.com/mlmhl))
* kubernetes-master charm now supports metrics server for horizontal pod autoscaler. ([#60174](https://github.com/kubernetes/kubernetes/pull/60174), [@hyperbolic2346](https://github.com/hyperbolic2346))
* Add ipset and udevadm to the hyperkube base image. ([#61357](https://github.com/kubernetes/kubernetes/pull/61357), [@rphillips](https://github.com/rphillips))
* In a GCE cluster, the default `HAIRPIN_MODE` is now "hairpin-veth". ([#60166](https://github.com/kubernetes/kubernetes/pull/60166), [@rramkumar1](https://github.com/rramkumar1))
* Balanced resource allocation priority in scheduler to include volume count on node  ([#60525](https://github.com/kubernetes/kubernetes/pull/60525), [@ravisantoshgudimetla](https://github.com/ravisantoshgudimetla))
* new dhcp-domain parameter to be used for figuring out the hostname of a node ([#61890](https://github.com/kubernetes/kubernetes/pull/61890), [@dims](https://github.com/dims))
* The node authorizer now automatically sets up rules for Node.Spec.ConfigSource when the DynamicKubeletConfig feature gate is enabled. ([#60100](https://github.com/kubernetes/kubernetes/pull/60100), [@mtaufen](https://github.com/mtaufen))
* Disable ipamperf integration tests as part of every PR verification. ([#61863](https://github.com/kubernetes/kubernetes/pull/61863), [@satyasm](https://github.com/satyasm))
* Enable server-side print in kubectl by default, with the ability to turn it off with --server-print=false ([#61477](https://github.com/kubernetes/kubernetes/pull/61477), [@soltysh](https://github.com/soltysh))
* Updated admission controller settings for Juju deployed Kubernetes clusters ([#61427](https://github.com/kubernetes/kubernetes/pull/61427), [@hyperbolic2346](https://github.com/hyperbolic2346))
* Performance test framework and basic tests for the IPAM controller, to simulate behavior ([#61143](https://github.com/kubernetes/kubernetes/pull/61143), [@satyasm](https://github.com/satyasm))
    * of the four supported modes under lightly loaded and loaded conditions, where load is
    * defined as the number of operations to perform as against the configured kubernetes
    * API server QPS.
* Automatically add system critical priority classes at cluster boostrapping. ([#60519](https://github.com/kubernetes/kubernetes/pull/60519), [@bsalamat](https://github.com/bsalamat))
* Removed always pull policy from the template for ingress on CDK. ([#61598](https://github.com/kubernetes/kubernetes/pull/61598), [@hyperbolic2346](https://github.com/hyperbolic2346))
* `make test-cmd` now works on OSX. ([#61393](https://github.com/kubernetes/kubernetes/pull/61393), [@totherme](https://github.com/totherme))
* Conformance: ReplicaSet must be supported in the `apps/v1` version. ([#61367](https://github.com/kubernetes/kubernetes/pull/61367), [@enisoc](https://github.com/enisoc))
* Remove 'system' prefix from Metadata Agent rbac configuration ([#61394](https://github.com/kubernetes/kubernetes/pull/61394), [@kawych](https://github.com/kawych))
* Support new NODE_OS_DISTRIBUTION 'custom' on GCE ([#61235](https://github.com/kubernetes/kubernetes/pull/61235), [@yguo0905](https://github.com/yguo0905))
    * on a new add event.
* include file name in the error when visiting files ([#60919](https://github.com/kubernetes/kubernetes/pull/60919), [@dixudx](https://github.com/dixudx))
* Split PodPriority and PodPreemption feature gate (#62243, @resouer)
* Code generated for CRDs now passes `go vet`. (#62412, @bhcleek)
* "beginPort+offset" format support for port range which affects kube-proxy only ([#58731](https://github.com/kubernetes/kubernetes/pull/58731), [@yue9944882](https://github.com/yue9944882))
* Added e2e test for watch ([#60331](https://github.com/kubernetes/kubernetes/pull/60331), [@jennybuckley](https://github.com/jennybuckley))
* kubeadm: prompt the user for confirmation when resetting a master node (#59115, @alexbrand)
* add warnings on using pod-infra-container-image for remote container runtime (#62982, @dixudx)
* kubeadm creates kube-proxy with a toleration to run on all nodes, no matter the taint. (#62390, @discordianfish)
* Mount additional paths required for a working CA root, for setups where /etc/ssl/certs doesn't contains certificates but just symlink. (#59122, @klausenbusk)
* Introduce truncating audit backend that can be enabled for existing backend to limit the size of individual audit events and batches of events. (#61711, @crassirostris)
* Modify the kubeadm upgrade DAG for the TLS Upgrade (#62655, @stealthybox)
* stop kubelet to cloud provider integration potentially wedging kubelet sync loop (#62543, @ingvagabund)
* Set pod status to "Running" if there is at least one container still reporting as "Running" status and others are "Completed". (#62642, @ceshihao)

## Bug Fixes

### Pending

#### User Facing

* Fixes issue where PersistentVolume.NodeAffinity.NodeSelectorTerms were ANDed instead of ORed. ([#62556](https://github.com/kubernetes/kubernetes/pull/62556), [@msau42](https://github.com/msau42))
* Fix potential infinite loop that can occur when NFS PVs are recycled. ([#62572](https://github.com/kubernetes/kubernetes/pull/62572), [@joelsmith](https://github.com/joelsmith))
* Fixed column alignment when kubectl get is used with custom columns from OpenAPI schema ([#56629](https://github.com/kubernetes/kubernetes/pull/56629), [@luksa](https://github.com/luksa))
* kubectl: restore the ability to show resource kinds when displaying multiple objects ([#61985](https://github.com/kubernetes/kubernetes/pull/61985), [@liggitt](https://github.com/liggitt))
* Fixed a panic in `kubectl run --attach ...` when the api server failed to create the runtime object (due to name conflict, PSP restriction, etc.) ([#61713](https://github.com/kubernetes/kubernetes/pull/61713), [@mountkin](https://github.com/mountkin))
* kube-scheduler has been fixed to use `--leader-elect` option back to true (as it was in previous versions) ([#59732](https://github.com/kubernetes/kubernetes/pull/59732), [@dims](https://github.com/dims))
* kubectl: fixes issue with `-o yaml` and `-o json` omitting kind and apiVersion when used with `--dry-run` ([#61808](https://github.com/kubernetes/kubernetes/pull/61808), [@liggitt](https://github.com/liggitt))
* Ensure reasons end up as comments in `kubectl edit`. ([#60990](https://github.com/kubernetes/kubernetes/pull/60990), [@bmcstdio](https://github.com/bmcstdio))

#### General Fixes and Reliability

* Fixed #731 kubeadm upgrade ignores HighAvailability feature gate ([#62455](https://github.com/kubernetes/kubernetes/pull/62455), [@fabriziopandini](https://github.com/fabriziopandini))
* Schedule even if extender is not available when using extender  ([#61445](https://github.com/kubernetes/kubernetes/pull/61445), [@resouer](https://github.com/resouer))
* Fix panic create/update CRD when mutating/validating webhook configured. ([#61404](https://github.com/kubernetes/kubernetes/pull/61404), [@hzxuzhonghu](https://github.com/hzxuzhonghu))
* Pods requesting resources prefixed with `*kubernetes.io` will remain unscheduled if there are no nodes exposing that resource. ([#61860](https://github.com/kubernetes/kubernetes/pull/61860), [@mindprince](https://github.com/mindprince))
* fix scheduling policy on ConfigMap breaks without the --policy-configmap-namespace flag set ([#61388](https://github.com/kubernetes/kubernetes/pull/61388), [@zjj2wry](https://github.com/zjj2wry))
* fix sorting taints in case the sorting keys are equal ([#61255](https://github.com/kubernetes/kubernetes/pull/61255), [@dixudx](https://github.com/dixudx))
* fix sorting tolerations in case the keys are equal ([#61252](https://github.com/kubernetes/kubernetes/pull/61252), [@dixudx](https://github.com/dixudx))
* Bugfix for erroneous upgrade needed messaging in kubernetes worker charm. ([#60873](https://github.com/kubernetes/kubernetes/pull/60873), [@wwwtyro](https://github.com/wwwtyro))
* Fix inter-pod anti-affinity check to consider a pod a match when all the anti-affinity terms match. ([#62715](https://github.com/kubernetes/kubernetes/pull/62715), [@bsalamat](https://github.com/bsalamat))
* GCE: Bump GLBC version to 1.1.1 - fixing an issue of handling multiple certs with identical certificates ([#62751](https://github.com/kubernetes/kubernetes/pull/62751), [@nicksardo](https://github.com/nicksardo))
* Pod affinity `nodeSelectorTerm.matchExpressions` may now be empty, and works as previously documented: nil or empty `matchExpressions` matches no objects in scheduler. ([#62448](https://github.com/kubernetes/kubernetes/pull/62448), [@k82cn](https://github.com/k82cn))
* Fix an issue in inter-pod affinity predicate that cause affinity to self being processed incorrectly ([#62591](https://github.com/kubernetes/kubernetes/pull/62591), [@bsalamat](https://github.com/bsalamat))
* fix WaitForAttach failure issue for azure disk ([#62612](https://github.com/kubernetes/kubernetes/pull/62612), [@andyzhangx](https://github.com/andyzhangx))
* Fix user visible files creation for windows ([#62375](https://github.com/kubernetes/kubernetes/pull/62375), [@feiskyer](https://github.com/feiskyer))
* Fix machineID getting for vmss nodes when using instance metadata ([#62611](https://github.com/kubernetes/kubernetes/pull/62611), [@feiskyer](https://github.com/feiskyer))
* Fix Forward chain default reject policy for IPVS proxier ([#62007](https://github.com/kubernetes/kubernetes/pull/62007), [@m1093782566](https://github.com/m1093782566))
* fix nsenter GetFileType issue in containerized kubelet ([#62467](https://github.com/kubernetes/kubernetes/pull/62467), [@andyzhangx](https://github.com/andyzhangx))
* Ensure expected load balancer is selected for Azure ([#62450](https://github.com/kubernetes/kubernetes/pull/62450), [@feiskyer](https://github.com/feiskyer))
* Resolves forbidden error when the `daemon-set-controller` cluster role access `controllerrevisions` resources. ([#62146](https://github.com/kubernetes/kubernetes/pull/62146), [@frodenas](https://github.com/frodenas))
* kubeadm: surface external etcd preflight validation errors ([#60585](https://github.com/kubernetes/kubernetes/pull/60585), [@alexbrand](https://github.com/alexbrand))
* fix incompatible file type checking on Windows ([#62154](https://github.com/kubernetes/kubernetes/pull/62154), [@dixudx](https://github.com/dixudx))
* fix local volume absolute path issue on Windows ([#62018](https://github.com/kubernetes/kubernetes/pull/62018), [@andyzhangx](https://github.com/andyzhangx))
* fix the issue that default azure disk fsypte(ext4) does not work on Windows ([#62250](https://github.com/kubernetes/kubernetes/pull/62250), [@andyzhangx](https://github.com/andyzhangx))
* Fixed bug in rbd-nbd utility when nbd is used. ([#62168](https://github.com/kubernetes/kubernetes/pull/62168), [@piontec](https://github.com/piontec))
* fix local volume issue on Windows ([#62012](https://github.com/kubernetes/kubernetes/pull/62012), [@andyzhangx](https://github.com/andyzhangx))
* Fix a bug that fluentd doesn't inject container logs for CRI container runtimes (containerd, cri-o etc.) into elasticsearch on GCE. ([#61818](https://github.com/kubernetes/kubernetes/pull/61818), [@Random-Liu](https://github.com/Random-Liu))
* flexvolume: trigger plugin init only for the relevant plugin while probe ([#58519](https://github.com/kubernetes/kubernetes/pull/58519), [@linyouchong](https://github.com/linyouchong))
* Fixed ingress issue with CDK and pre-1.9 versions of kubernetes. ([#61859](https://github.com/kubernetes/kubernetes/pull/61859), [@hyperbolic2346](https://github.com/hyperbolic2346))
* Fix racy panics when using fake watches with ObjectTracker ([#61195](https://github.com/kubernetes/kubernetes/pull/61195), [@grantr](https://github.com/grantr))
* Fix mounting of UNIX sockets(and other special files) in subpaths ([#61480](https://github.com/kubernetes/kubernetes/pull/61480), [@gnufied](https://github.com/gnufied))
* Fixed [#61123](https://github.com/kubernetes/kubernetes/pull/61123) by triggering syncer.Update on all cases including when a syncer is created ([#61124](https://github.com/kubernetes/kubernetes/pull/61124), [@satyasm](https://github.com/satyasm))
* Fix data race in node lifecycle controller ([#60831](https://github.com/kubernetes/kubernetes/pull/60831), [@resouer](https://github.com/resouer))
* fix resultRun by resetting it to 0 on pod restart (#62853, @tony612)
* Fix the liveness probe to use `/bin/bash -c` instead of `/bin/bash c`. (#63033, @bmoyles0117)
* Fix scheduler informers to receive events for all the pods in the cluster. (#63003, @bsalamat)
* Fix in vSphere Cloud Provider to handle upgrades from kubernetes version less than v1.9.4 to v1.9.4 and above. (#62919, @abrarshivani)
* Fix error where config map for Metadata Agent was not created by addon manager. (#62909, @kawych)
* Fixes the kubernetes.default.svc loopback service resolution to use a loopback configuration. (#62649, @liggitt)
* fix permissions to allow statefulset scaling for admins, editors, and viewers (#62336, @deads2k)
* GCE: Fix for internal load balancer management resulting in backend services with outdated instance group links. (#62885, @nicksardo)
* Deployment will stop adding pod-template-hash labels/selector to ReplicaSets and Pods it adopts. Resources created by Deployments are not affected (will still have pod-template-hash labels/selector).  ([#61615](https://github.com/kubernetes/kubernetes/pull/61615), [@janetkuo](https://github.com/janetkuo))
* Use inline func to ensure unlock is executed ([#61644](https://github.com/kubernetes/kubernetes/pull/61644), [@resouer](https://github.com/resouer))
* Ensure cloudprovider.InstanceNotFound is reported when the VM is not found on Azure ([#61531](https://github.com/kubernetes/kubernetes/pull/61531), [@feiskyer](https://github.com/feiskyer))
* CRI: define the mount behavior when host path does not exist: runtime should report error if the host path doesn't exist ([#61460](https://github.com/kubernetes/kubernetes/pull/61460), [@feiskyer](https://github.com/feiskyer))
* kubernetes-master charm now properly clears the client-ca-file setting on the apiserver snap ([#61479](https://github.com/kubernetes/kubernetes/pull/61479), [@hyperbolic2346](https://github.com/hyperbolic2346))
* Bound cloud allocator to 10 retries with 100 ms delay between retries. ([#61375](https://github.com/kubernetes/kubernetes/pull/61375), [@satyasm](https://github.com/satyasm))
* respect fstype in Windows for azure disk ([#61267](https://github.com/kubernetes/kubernetes/pull/61267), [@andyzhangx](https://github.com/andyzhangx))
* Unready pods will no longer impact the number of desired replicas when using horizontal auto-scaling with external metrics or object metrics. ([#60886](https://github.com/kubernetes/kubernetes/pull/60886), [@mattjmcnaughton](https://github.com/mattjmcnaughton))
* Nodes are not deleted from kubernetes anymore if node is shutdown in Openstack. ([#59931](https://github.com/kubernetes/kubernetes/pull/59931), [@zetaab](https://github.com/zetaab))
* authz: nodes should not be able to delete themselves (#62818, @mikedanese)
* removed unsafe double RLock in cpumanager (#62464, @choury)


