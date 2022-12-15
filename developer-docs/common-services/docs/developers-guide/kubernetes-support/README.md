---
description: How to manage Kubernetes within the Unity platform
---

# Kubernetes Support

We currently support managed versions of the 1.21 Kubernetes platform via the EKS product supplied by AWS. These clusters can be deployed by developers and administrators of the Unity platform both as part of a wider deployment and also "standalone" to develop against.

In line with the access control support in the Unity platform, we expect production deployments of Kubernetes to be installed into our Private Subnets and access either internally or via the API gateway that can then expose endpoints in a security and managed fashion.

