Day 60 — Quick Summary
Created a capstone namespace for the application.
Deployed MySQL with StatefulSet, Secret, Headless Service, and persistent storage.
Deployed WordPress with Deployment, ConfigMap, Secrets, resources, and probes.
Exposed WordPress using a NodePort Service.
Tested self-healing by deleting WordPress and MySQL Pods.
Verified data persistence after MySQL Pod recreation.
Added an HPA for automatic WordPress scaling.
Optionally compared the setup with Helm.
Cleaned up the namespace and all resources.
Documented the architecture, tests, and lessons learned in day-60-capstone.md.

Key takeaway: You combined the major Kubernetes concepts from Days 51–59 into one real-world application stack.

I can also give you the 
complete YAML manifests and a 
ready-to-submit day-60-capstone.md.
