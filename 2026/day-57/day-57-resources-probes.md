Day 57 — Quick Summary
Set CPU and memory requests/limits and learned QoS classes.
Observed OOMKilled (exit code 137) when memory exceeded its limit.
Created a Pending Pod due to insufficient requested resources.
Tested liveness probes → restart unhealthy containers.
Tested readiness probes → remove unhealthy Pods from Service endpoints without restarting.
Tested startup probes → give slow containers time to initialize.
Learned CPU is throttled, while excessive memory can cause OOMKilled.

Key takeaway: Requests help Kubernetes schedule Pods, limits control resource usage, and probes enable automatic health management.

I can also provide the required YAML manifests and a short day-57-resources-probes.md.
