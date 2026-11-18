# Cross-cluster live migration with Red Hat Advanced Cluster Management 5.0: Plan the destination before the move

Moving a running virtual machine to another cluster can help you prepare for a hardware refresh or free capacity for maintenance. For a platform team, the work starts before the move: identifying a suitable destination, preparing its networks and storage, and checking that users can still reach the application afterward.

Red Hat Advanced Cluster Management for Kubernetes (ACM) brings these operations into a fleet management context. With ACM 5.0, **capacity recommendations for virtual machine cross-cluster live migration are available as a Technology Preview**, adding visibility into destination storage, CPU, and memory before a migration begins. That information helps operators identify resource constraints earlier. [Read the ACM 5.0 announcement.](https://www.redhat.com/en/blog/red-hat-advanced-cluster-management-kubernetes-5)

For virtualization administrators and platform engineers, the useful question is broader than whether a VM can move: Is the destination ready to run the workload and keep its service available?

## What changes with ACM 5.0?

Cross-cluster live migration predates ACM 5.0. Red Hat announced general availability with ACM 2.16 and OpenShift Virtualization 4.21. The 5.0 story builds on that capability with more information for migration planning; it should not be confused with the original availability announcement. [See the ACM 2.16 announcement](https://www.redhat.com/en/blog/stop-searching-start-operating-scale-hybrid-clusters-red-hat-advanced-cluster-management-kubernetes-216) and [OpenShift Virtualization 4.21 announcement](https://www.redhat.com/en/blog/whats-new-red-hat-openshift-virtualization-421).

Keep these distinctions in mind when evaluating the release:

| Capability | How to interpret it |
|---|---|
| Cross-cluster live migration | An existing capability for moving running VMs between supported OpenShift Virtualization environments. |
| ACM 5.0 destination-capacity recommendations | A Technology Preview to help assess target storage, CPU, and memory. |
| A successful application migration | An outcome to validate across compute, storage, guest networking, and application dependencies. |

Capacity guidance can inform an operator's decision. Treat resource reservation, automatic destination selection, and autonomous fleet rebalancing as separate capabilities that require their own documentation and validation.

## Where cross-cluster mobility helps

A practical starting point is a planned infrastructure change. Suppose your team needs to retire a cluster after a hardware refresh. Rather than making the entire VM estate part of one shutdown event, consider a staged process: prepare the destination, select a representative workload, validate its migration, and expand in controlled batches.

The same approach can help with deliberate capacity redistribution. Review which workloads could move, which destinations meet their requirements, and what service checks will establish success. Define the acceptance criteria before the first migration so each move produces evidence for the next.

Keep a recovery plan alongside the migration plan. A planned move assumes that the source and migration path remain usable long enough to transfer the workload. For a source failure or an unavailable site, evaluate the appropriate backup, restore, and disaster recovery procedures separately.

## How the migration layers work together

ACM, the migration toolkit for virtualization (MTV), and OpenShift Virtualization have complementary responsibilities. ACM provides the fleet context and integration; MTV coordinates the migration workflow; OpenShift Virtualization performs the VM state transfer.

MTV's documented workflow prepares destination volumes and VM resources, copies mounted secrets and configuration maps, and creates migration resources on the participating clusters. KubeVirt handles the transfer. This division is useful when troubleshooting: determine whether a problem concerns preparation, orchestration, or runtime transfer before changing configuration. [See the MTV live migration workflow.](https://docs.redhat.com/en/documentation/migration_toolkit_for_virtualization/2.12/pdf/planning_your_migration_to_red_hat_openshift_virtualization/index)

Underneath that workflow, decentralized live migration coordinates separate source and destination VM instances. Paired `VirtualMachineInstanceMigration` resources identify the sending and receiving sides, and synchronization controllers coordinate their state. The destination VM waits as a receiver while the migration is prepared. These upstream mechanics explain the architecture; use the supported product workflow to operate it. [See KubeVirt's decentralized live migration guide.](https://kubevirt.io/user-guide/compute/decentralized_live_migration/)

## Prepare the destination across four areas

### 1. Validate the supported software combination

Record the exact ACM, OpenShift, OpenShift Virtualization, and MTV versions in your environment. Validate the combination against the [ACM 5.0 support matrix](https://access.redhat.com/articles/7149098) and the applicable component documentation.

Avoid treating a minimum version followed by a plus sign as a compatibility guarantee. A useful migration record identifies both endpoints and the actual supported combination, including patch levels and migration direction. Recheck it when an operator or platform version changes.

### 2. Prepare storage and runtime resources

Check that the destination can provision the required disks and satisfy the VM's scheduling requirements. As an operational practice, review storage mappings, quotas, placement constraints, and the capacity that must remain available for other workloads.

If you evaluate ACM 5.0 capacity recommendations, include their findings in this review while retaining their Technology Preview status. A capacity signal is one input to the decision; your acceptance test should still demonstrate that the selected workload can run as expected.

### 3. Plan the migration network and application network separately

The migration network carries the transfer. The guest network carries application traffic. Validate both paths, and document how the application will remain reachable at its destination.

For the transfer path, follow the topology and dedicated-network instructions for your installed OpenShift Virtualization release. For the service path, explicitly test guest addressing, DNS, external access, and dependent systems. A network name or matching subnet alone is insufficient evidence of application continuity.

MTV does not establish intercluster connectivity or migrate unrelated resources such as services and routes. Include those dependencies in the wider change plan. [Review MTV's live migration limitations.](https://docs.redhat.com/en/documentation/migration_toolkit_for_virtualization/2.12/pdf/planning_your_migration_to_red_hat_openshift_virtualization/index)

### 4. Verify identity and permissions

The ACM 5.0 migration procedure covers hub integration, participating-cluster labels, and `ClusterPermission` configuration. It also identifies fleet access, extended VM administration, migration readiness, and core virtualization permissions. Follow those steps before testing with the identity that will perform the migration. [See the ACM 5.0 preparation and migration procedures.](https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_management_for_kubernetes/5.0/html/virtualization/acm-virt)

Test with the intended operational identity as well as an administrator. This helps distinguish a workload or infrastructure issue from an authorization issue before a production change.

## Use configuration that matches the managed-cluster version

The ACM hub version and the managed cluster's virtualization configuration are separate considerations. When using OpenShift Virtualization 5, the documented tuning example uses `hco.kubevirt.io/v1` and nests migration settings under `spec.virtualization.liveMigrationConfig`. Older examples can use a different structure.

For example, this partial configuration shows the OpenShift Virtualization 5 field hierarchy:

```yaml
apiVersion: hco.kubevirt.io/v1
kind: HyperConverged
metadata:
  name: kubevirt-hyperconverged
  namespace: openshift-cnv
spec:
  virtualization:
    liveMigrationConfig:
      allowPostCopy: false
```

This is a configuration excerpt, not a complete resource to replace your existing configuration. Inspect the installed schema and follow the instructions for your managed-cluster version before changing it.

Tune against measurements from representative workloads. In particular, high memory write rates can make migration harder to complete. Red Hat cautions against post-copy for critical data or unstable networks, so it should not be a blanket recommendation for database migrations. [Review OpenShift Virtualization 5 migration configuration and tuning guidance.](https://docs.redhat.com/en/documentation/openshift_container_platform/5.0/html/virtualization/live-migration)

## Make the first migration a measured rehearsal

Choose a representative, non-production VM and write down what success means. Include an application transaction, not just a VM status check.

1. **Capture the baseline.** Record application response times, error rates, resource use, and connectivity.
2. **Review readiness.** Confirm compatibility, authorization, resource availability, storage mappings, and network reachability.
3. **Run the supported migration workflow.** Observe preparation and transfer progress on both endpoints.
4. **Validate the service.** Repeat the application transaction and test access from the same client paths used before migration.
5. **Review recovery and cleanup.** Confirm the resulting resource state before expanding to another workload or migration batch.

If you are upgrading from ACM 2.17, also review the ACM 5.0 known issue involving the `kubevirt-hyperconverged` add-on and verify that the integration is healthy before rehearsing migrations. [See the ACM 5.0 release notes.](https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_management_for_kubernetes/5.0/html/release_notes/acm-release-notes)

## Start with one workload

Use the [ACM 5.0 migration guide](https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_management_for_kubernetes/5.0/html/virtualization/acm-virt) to prepare two supported clusters, then rehearse a move with one representative VM. Measure the application before, during, and after migration, and use the results to shape the next batch.

That gives your team a repeatable basis for cluster maintenance and hardware changes: a prepared destination, a verified migration path, and evidence that the service still works when the move is complete.
