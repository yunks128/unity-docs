---
description: Unity leverages a set of parameters that define various things
---

# Unity (SSM) Parameters

Unity services (both shared services and venue services) leverage a set of defined and globally accessible (from within a venue) parameters.  The parameters are conveniently stored in AWS SSM, and can be accessed in a repeatable, venue-agnostic way.  These are used to share information between components, and serves as a central registry of Unity information.

See the following pages:

[ssm-parameter-naming-requirements-and-conventions.md](ssm-parameter-naming-requirements-and-conventions.md "mention")

[standard-set-of-ssm-parameters.md](standard-set-of-ssm-parameters.md "mention")

[deployments-dependency-and-ssm-parameters.md](deployments-dependency-and-ssm-parameters.md "mention")
