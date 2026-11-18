# Make VM mobility a fleet operation with Red Hat Advanced Cluster Management 5.0

A hardware refresh should give your infrastructure team more options. But when applications depend on virtual machines spread across several clusters, even a planned change can become a coordination exercise: finding the affected VMs, preparing another cluster, arranging access, and keeping services available throughout the move.

Cross-cluster live migration gives teams a way to move running VMs between supported Red Hat OpenShift Virtualization environments. Red Hat Advanced Cluster Management for Kubernetes (RHACM) brings that capability into the way teams manage their fleet.

With RHACM 5.0, administrators can approach VM mobility through centralized visibility, consistent cluster preparation, controlled access, and an integrated migration workflow. Together, these capabilities help make infrastructure changes easier to plan and repeat.

## Start with a fleet view

Consider a team preparing to retire an older virtualization cluster. Before moving anything, administrators need to understand which VMs run there and which workloads belong in the first migration batch.

RHACM provides a central view of virtual machines across managed clusters. Administrators can discover and filter VM resources, inspect their details, and perform supported lifecycle operations without starting a separate investigation in every cluster. This gives the team a shared starting point for planning the change. [Explore RHACM’s virtualization management capabilities.](https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_management_for_kubernetes/5.0/html/virtualization/acm-virt)

That visibility becomes more useful as the environment grows. A cluster maintenance task can be planned in the context of the wider VM estate, with a clearer understanding of the workloads involved.

## Prepare clusters consistently

Installing a virtualization operator is one part of preparing a migration destination. The participating clusters also need the appropriate migration configuration.

For example, depending on the supported component versions, cross-cluster live migration can require configuration in both the migration toolkit for virtualization (MTV) and OpenShift Virtualization. The source and destination must have the required capabilities active before a migration can proceed.

RHACM’s policy framework gives platform teams a way to manage that preparation consistently. Teams can define the expected configuration, target selected clusters, and check whether those clusters match it. Where appropriate, configured policies can also enforce the desired state. [Learn about RHACM’s fleet management and policy capabilities.](https://www.redhat.com/en/technologies/management/advanced-cluster-management)

Applied to migration preparation, this provides a practical operating pattern:

- Define a supported virtualization and migration baseline.
- Apply it to the clusters intended to participate.
- Review configuration differences before scheduling a move.
- Reuse the baseline when another destination joins the fleet.

The operational benefit carries beyond one migration. The configuration becomes something the platform team can manage throughout the clusters’ lifecycle.

## Give operators the access they need

VM mobility often crosses team boundaries. A platform team manages the clusters, while application or virtualization administrators manage workloads within specific projects.

RHACM supports fine-grained access for fleet virtualization operations. Fleet access, VM administration, and migration readiness checks have distinct permissions, allowing teams to assign responsibilities at the appropriate scope. These permissions work alongside the underlying OpenShift Virtualization roles. [Review the virtualization access model.](https://docs.redhat.com/fr/documentation/red_hat_advanced_cluster_management_for_kubernetes/2.16/pdf/secure_clusters/rhacm-rbac-implement)

For an organization with several application teams, this creates a more useful way to delegate operations. Administrators can work within defined boundaries, while the platform team retains control over the wider environment.

As migration becomes a recurring maintenance activity, those boundaries help teams establish who prepares the environment, who performs the move, and who validates the application.

## Bring migration into the fleet workflow

Once the environment is prepared, the RHACM Fleet Virtualization experience brings cross-cluster migration into the same management context used to work with the VM estate.

The components have complementary responsibilities. RHACM provides fleet visibility, access control, and migration integration. MTV coordinates the migration plan and resource preparation. OpenShift Virtualization performs the underlying live transfer.

RHACM’s documented integration can install MTV on the hub and OpenShift Virtualization on designated managed clusters. This helps platform teams organize the supporting components through their existing management environment. [See the RHACM migration preparation procedure.](https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_management_for_kubernetes/5.0/html/virtualization/acm-virt)

For the operator, the value is a connected process: identify the workload, work through the migration workflow, and validate the result with the participating clusters already under fleet management.

## Make better destination decisions with RHACM 5.0

A destination may have the right software installed and still lack the resources a workload needs.

RHACM 5.0 introduces **capacity recommendations for VM cross-cluster live migration as a Technology Preview**. These recommendations check destination storage, CPU, and memory before migration begins, helping operators identify capacity constraints earlier. [Read the RHACM 5.0 announcement.](https://www.redhat.com/en/blog/red-hat-advanced-cluster-management-kubernetes-5)

For a team planning a hardware refresh, that additional information supports a better-informed choice of destination. It also helps explain why a proposed move may encounter resource limitations before the team commits to a larger migration batch.

The recommendations complement compatibility checks and operational testing. Their Technology Preview status should remain part of any evaluation plan.

## Put the capabilities together

Return to the cluster retirement scenario. A practical RHACM-led approach could look like this:

1. **Find the affected workloads.** Use fleet visibility to identify the VMs on the cluster being retired.
2. **Prepare the participating clusters.** Apply the agreed configuration baseline and review its compliance.
3. **Assign operational access.** Give the migration team the required fleet and workload permissions.
4. **Evaluate the destination.** Review compatibility, storage and network mappings, and available capacity.
5. **Move and validate.** Start with a representative workload, verify application behavior, and use the result to plan subsequent batches.

This approach connects the migration to the work around it. The team has a consistent method for preparing environments and performing the next infrastructure change.

The same pattern can support planned maintenance, cluster consolidation, and deliberate workload redistribution.

## Build mobility into everyday operations

Cross-cluster live migration depends on a supported combination of software, workload configuration, storage, and network connectivity. Application reachability also needs explicit attention: moving a VM does not automatically move every service, route, or external dependency associated with it. [Review MTV’s migration scope and limitations.](https://docs.redhat.com/en/documentation/migration_toolkit_for_virtualization/2.12/pdf/planning_your_migration_to_red_hat_openshift_virtualization/index)

RHACM adds value by bringing visibility, configuration management, permissions, and migration integration into a common operating approach. Teams can use that foundation to make VM mobility a planned, repeatable part of managing their infrastructure.

Start with two supported clusters and one representative VM. Follow the [RHACM 5.0 migration guide](https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_management_for_kubernetes/5.0/html/virtualization/acm-virt), validate the application throughout the move, and build a process your team can confidently reuse for its next maintenance cycle or hardware refresh.
