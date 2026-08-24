Day 58 — Quick Summary
Installed Metrics Server and used kubectl top to monitor CPU/memory.
Created a Deployment with CPU requests for HPA.
Created an HPA with min 1 and max 10 replicas.
Generated load and observed automatic scaling.
Created an HPA declaratively using autoscaling/v2.
Learned how HPA calculates desired replicas and controls scale-up/scale-down behavior.
Cleaned up the test resources while keeping Metrics Server installed.

Key takeaway: Metrics Server provides usage data, while HPA automatically adjusts Pod replicas based on resource utilization.

I can also give you the HPA YAML and 
a short day-58-metrics-hpa.md for submission.
