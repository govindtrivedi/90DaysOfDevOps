Day 55 — Quick Summary
Demonstrated that Pod/container data is ephemeral using emptyDir.
Created a PersistentVolume (PV) with hostPath.
Created a PersistentVolumeClaim (PVC) and bound it to the PV.
Mounted the PVC into a Pod and verified data survives Pod deletion.
Learned RWO, ROX, and RWX access modes.
Explored StorageClasses and dynamic provisioning.
Compared Retain vs Delete reclaim policies.
Cleaned up Pods, PVCs, and PVs.

Key takeaway: Pods are temporary, but PV + PVC provide persistent storage beyond the Pod lifecycle.

I can also give you the required PV, PVC, and Pod YAML files plus 
a short day-55-persistent-volumes.md.
