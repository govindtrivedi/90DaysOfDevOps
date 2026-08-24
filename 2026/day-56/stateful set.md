Day 56 — Quick Summary
Compared Deployments vs StatefulSets for stateful applications.
Created a Headless Service with clusterIP: None.
Created a StatefulSet with 3 replicas and stable names: web-0, web-1, web-2.
Created per-Pod PVCs using volumeClaimTemplates.
Tested stable Pod DNS and verified it matches Pod IPs.
Verified data persistence after Pod deletion.
Practiced ordered scaling up/down.
Learned PVCs remain after StatefulSet deletion or scale-down.

Key takeaway: StatefulSets provide stable identity, ordered operations, stable DNS, and dedicated persistent storage—making them suitable for databases and other stateful workloads.

I can also provide the StatefulSet, Headless Service, and PVC YAML files plus 
a short day-56-statefulsets.md.
