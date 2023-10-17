# Unity AWS Resource Tagging Conventions



#### WORK IN PROGRESS (DON'T USE FOR OFFICIAL PURPOSES YET!)

**Version 1**

### Introduction

Amazon Web Services provides the ability to assign metadata to AWS resources in the form of **tags**. Unity has compiled a list of tags that we believe will be needed to allow the team to easily categorize resources by purpose, owner, environment, and other specific criteria.  **Also, importantly, these tags allow for tracking of how much money is spent on the various AWS resources.**  This document describes Unity tagging convention and pattern for various projects.

This is a living document, and may be subjected to change as requirements may change during the developmental cycle.

### Table of Contents

1. [General Guidelines](unity-aws-resource-tagging-conventions.md#ggl)
2. [Tagging Categories](unity-aws-resource-tagging-conventions.md#tc)
3. [Mandatory Tags](unity-aws-resource-tagging-conventions.md#mbt)
4. [Optional Tags](unity-aws-resource-tagging-conventions.md#mbt-1)
5. [Abbreviation Conventions](unity-aws-resource-tagging-conventions.md#ac)
6. [Resource Specific Tags](unity-aws-resource-tagging-conventions.md#rst)

### General Guidelines <a href="#ggl" id="ggl"></a>

* All keys shall adhere to the camelcase convention
* All values shall be in lowercase
* Tags can only be in alphanumeric, no special characters (with the exception of @)
* Shall make use of abbreviations as much as possible for complex tag patterns
* There shall be a mandatory set of tags that will be applied to all (applicable) AWS resources
* The amount of tags will differ between resources with respect to tagging limitations for that resources and special requirements
* All configuration/schema/resources should notate the version of this document for configuration management

### Tagging Categories <a href="#tc" id="tc"></a>

* **Technical**: For consolidating and organizing AWS resources.
* **Automation**: User for filtering resources during infrastructure automation activities
* **Costing**: To help associate AWS costs to the users of the resource
* **Security**: For determining security configuration and compliance of AWS resources

### Mandatory  Tags <a href="#mbt" id="mbt"></a>

These tags should be provided in all AWS resources.

<table><thead><tr><th width="142">Name</th><th>Description</th></tr></thead><tbody><tr><td><a href="unity-aws-resource-tagging-conventions.md#venue">Venue</a></td><td>Venue that the resource is being deployed on.  Ex: <code>Dev</code>, <code>Test</code>, <code>SIPS-test</code></td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#servicearea">ServiceArea</a></td><td>Which service area is this related to? Ex: <code>sps</code>, <code>ds</code>, <code>as</code>, <code>ads</code>, <code>cs</code></td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#capability">Capability</a></td><td>Unity capability.  Ex: <code>processing</code>, <code>datastorage</code>, <code>analysis</code>, etc..</td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#capversion">CapVersion</a></td><td>The version number of the capability provided</td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#component">Component</a></td><td>Unity component.  Ex: <code>HySDS</code>, <code>Cumulus</code>, <code>SDAP</code>, <code>Dockstore</code>, etc..</td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#name">Name</a></td><td>Name of the resource -- <mark style="color:yellow;">MCP/Kion costing tag</mark></td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#proj">Proj</a></td><td>Unity Project Ex: <code>sips</code> or <code>unity</code> (shared service) -- <mark style="color:yellow;">MCP/Kion costing tag</mark></td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#createdby">CreatedBy</a></td><td>same as <a href="unity-aws-resource-tagging-conventions.md#servicearea">ServiceArea</a> (see above) -- <mark style="color:yellow;">MCP/Kion costing tag</mark></td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#env">Env</a></td><td>same as <a href="unity-aws-resource-tagging-conventions.md#venue">Venue</a> (see above) -- <mark style="color:yellow;">MCP/Kion costing tag</mark></td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#stack">Stack</a></td><td>same as <a href="unity-aws-resource-tagging-conventions.md#component">Component</a> (see above) -- <mark style="color:yellow;">MCP/Kion costing tag</mark></td></tr></tbody></table>

### Optional  Tags <a href="#mbt" id="mbt"></a>

These tags can be used to provide additional information and context about the AWS resources.

<table><thead><tr><th width="193">Name</th><th>Description</th></tr></thead><tbody><tr><td><a href="unity-aws-resource-tagging-conventions.md#poc">POC</a></td><td>A list of email(s) for the point of contact for the resource</td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#release">Release</a></td><td>The version number that this resource belongs to. Assumes semantic versioning.</td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#securityplanid">SecurityPlanID</a></td><td>IT Security Plan ID for the resource</td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#exposedweb">ExposedWeb</a></td><td>Will this resource be exposed to the web?</td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#experimental">Experimental</a></td><td>Is this an experimental resource?</td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#userfacing">UserFacing</a></td><td>Will this resource be user facing?</td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#critinfra">CritInfra</a></td><td>Is this resource a part of the critical infrastructure?</td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#sourcecontrol">SourceControl</a></td><td>Documentation or SCM link for resources deployed</td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#mission">mission</a></td><td>not needed <mark style="color:yellow;">MCP/Kion costing tag</mark></td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#mcpbilling">mcpBilling</a></td><td>not needed <mark style="color:yellow;">MCP/Kion costing tag</mark></td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#servicenow-instance">ServiceNow Instance</a></td><td>not needed <mark style="color:yellow;">MCP/Kion costing tag</mark></td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#station">station</a></td><td>not needed <mark style="color:yellow;">MCP/Kion costing tag</mark></td></tr><tr><td><a href="unity-aws-resource-tagging-conventions.md#customer">Customer</a></td><td>not needed <mark style="color:yellow;">MCP/Kion costing tag</mark></td></tr></tbody></table>

***

## Tag Specifications

## Name

| Type   | Example Value             | Required | Default Value      |
| ------ | ------------------------- | -------- | ------------------ |
| String | `foo-dev-sps-hysds-mid01` | True     | N/A - User Defined |

* Description: The name of the AWS resource. All characters need to be in lowercase.
* Pattern: `{Proj}-{Venue}-{ServiceArea}-{Capability}-{Component}`
* Pattern Components:
  * **Proj** :`(foo)` for the project abbreviation.
  * **Venue** :`([a-z0-9]{1,6})` See `Venue` below for more info. Limit 6 characters. Examples include dev, test, and prod.
  * **ServiceArea**:`(cs\|sps\|ds\|ads\|as)` See `ServiceArea` for more info. The service area abbreviation, or "all" if the resource is to be used by all service areas
  * **Capability**: `([a-z0-9]+)` for the name of the application. See `Capability` below for more info. Should be unique project wide.
  * **Component**: `([a-z0-9]+)` for the component that the application falls under. e.g. (backend) for backend, (ft) for frontend, etc. See `Component` below for more info.
* More examples:
  * `foo-dev-sps-hysds-mid01`
  * `foo-train1-rps-rsvp-ft01`

## CreatedBy

| Type   | Example Values          | Required | Default Value      |
| ------ | ----------------------- | -------- | ------------------ |
| String | `sps`, `ds`, `as`, `cs` | True     | N/A - User Defined |

* Description:  This tag is required by Kion/MCP for cost monitoring purposes, and should contain the **same value as the "ServiceArea" tag**.  See documentation [here](unity-aws-resource-tagging-conventions.md#servicearea).
* `Category: Technical, Costing`

## Customer

| Type   | Example Value        | Required | Default Value      |
| ------ | -------------------- | -------- | ------------------ |
| String | `Sounder SIPS Admin` | True     | N/A - User Defined |

* Description: The customer (who uses it) for the AWS resource
* Example:  <mark style="color:red;">TODO:</mark> [Galen Hollins](http://127.0.0.1:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
* `Category: Technical, Costing`

## Env

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `test`        | True     | N/A - User Defined |

* Description:  This tag is required by Kion/MCP for cost monitoring purposes, and should contain the **same value as the "Venue" tag**.  See documentation [here](unity-aws-resource-tagging-conventions.md#proj).
* `Category: Technical, Costing`

## mcpBilling

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `true`        | True     | N/A - User Defined |

* Description: <mark style="color:red;">TODO:</mark> [Galen Hollins](http://127.0.0.1:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
* Example:  <mark style="color:red;">TODO:</mark> [Galen Hollins](http://127.0.0.1:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
* `Category: Technical, Costing`

## mission

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `europa`      | True     | N/A - User Defined |

* Description:  This tag is required by Kion/MCP for cost monitoring purposes, and should contain the **same value as the "Proj" tag**.  See documentation [here](unity-aws-resource-tagging-conventions.md#proj).
* `Category: Technical, Costing`

## ServiceNow Instance

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | tbd           | True     | N/A - User Defined |

* `Category: Technical, Costing`
* Example:  <mark style="color:red;">TODO:</mark> [Galen Hollins](http://127.0.0.1:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
* Description: <mark style="color:red;">TODO:</mark> [Galen Hollins](http://127.0.0.1:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>

## Stack

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `U-AS`        | True     | N/A - User Defined |

* `Category: Technical, Costing`
* Example:  <mark style="color:red;">TODO:</mark> [Galen Hollins](http://127.0.0.1:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
* Description: <mark style="color:red;">TODO:</mark> [Galen Hollins](http://127.0.0.1:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>

## station

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `tbd`         | True     | N/A - User Defined |

* `Category: Technical, Costing`
* Example:  <mark style="color:red;">TODO:</mark> [Galen Hollins](http://127.0.0.1:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
* Description: <mark style="color:red;">TODO:</mark> [Galen Hollins](http://127.0.0.1:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>



## POC

| Type   | Example Value(s)                           | Required | Default Value      |
| ------ | ------------------------------------------ | -------- | ------------------ |
| String | `foo-sps@jpl.nasa.gov:foo-ds@jpl.nasa.gov` | True     | N/A - User Defined |

* Description: The list of the point of contacts that is responsible for the resource is being deployed on.
* Pattern: `{Poc}`
* Pattern Components:
  * **Poc**:`([a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+)` for the email of the creator of the resource.

## Venue

| Type   | Example Value              | Required | Default Value      |
| ------ | -------------------------- | -------- | ------------------ |
| String | `Dev`, `Test`, `SIPS-test` | True     | N/A - User Defined |

* Description: The name of the venue that the resource is being deployed on.

## Proj

| Type   | Example Values    | Required | Default Value | Category |
| ------ | ----------------- | -------- | ------------- | -------- |
| String | `europa`, `unity` | True     | `foo`         | Business |

* Description: The name of the project/mission (e.g. `europa`), or `unity` if it's a shared service
* Category: Technical, Costing

## ServiceArea

| Type   | Example Values                 | Required | Default Value      |
| ------ | ------------------------------ | -------- | ------------------ |
| String | `sps`, `ds`, `as`, `ads`, `cs` | True     | N/A - User Defined |

* Description: The name of the Unity service area.
* Pattern: `{ServiceArea}`
* Pattern Components:
  * **ServiceArea**:`(cs\|sps\|ds\|ads\|as)`|See `ServiceArea` for more info. The service area abbreviation, or "all" if the resource is to be used by all service areas
* Pattern Example:
  * `sps`

## Capability

| Type   | Example Value                            | Required | Default Value      |
| ------ | ---------------------------------------- | -------- | ------------------ |
| String | `processing`, `analysis`, `data-storage` | True     | N/A - User Defined |

* Description: The Unity capability

## CapVersion

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `1.0.1`       | True     | N/A - User Defined |

* Description: Version of the application.  Make sure to use [semantic versioning](https://semver.org/).
* Pattern: `{CapVersion}`
* Pattern Components:
  * **CapVersion**:`^(\d+\.)?(\d+\.)?(\*|\d+)$` for the capability version that is provided by the service area.

## Release

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `G3.0`        | True     | N/A - User Defined |

* Description: Release version that the application belongs to.
* Pattern: `{Release}`
* Pattern Components:
  * **Release**:`^G(\d+\.)?(\d+\.)?(\*|\d+)$` for the release version that is provided by the service area

## Component

| Type   | Example Value                 | Required | Default Value      |
| ------ | ----------------------------- | -------- | ------------------ |
| String | `cumulus`, `Jupyter`, `HySDS` | True     | N/A - User Defined |

* Description: The primary type of application/runtime that will be run on this resource.
* Pattern: `{Component}`
* Pattern Components:
  * **Component**:`([a-z0-9]+)` for the primary type of application/runtime that run or support for this resource.

## SecurityPlanID

| Type         | Example Value | Required | Default Value |
| ------------ | ------------- | -------- | ------------- |
| Unsigned Int | `644`         | True     | `644`         |

* Description: The JPL security plan ID that this resource falls under.
* Pattern: `{PlanId}`
* Pattern Components:
  * **PlanId**:`([0-9]+)` for JPL security plan ID for this resource.

## ExposedWeb

| Type    | Example Value | Required | Default Value |
| ------- | ------------- | -------- | ------------- |
| Boolean | `false`       | True     | `false`       |

* Description: Is this resource exposed to the web?
* Pattern: `{ExposedWeb}`
* Pattern Components:
  * **ExposedWeb**:`([true|false]{1})` for true or false

## Experimental

| Type    | Example Value | Required | Default Value |
| ------- | ------------- | -------- | ------------- |
| Boolean | `false`       | True     | `false`       |

* Description: Is this an experimental resource? If so, it will be removed after a period of time.
* Pattern: `{Experimental}`
* Pattern Components:
  * **Experiment**:`([true|false]{1})` for true or false

## UserFacing

| Type    | Example Value | Required | Default Value |
| ------- | ------------- | -------- | ------------- |
| Boolean | `false`       | True     | `false`       |

* Description: Is this resource user facing? Does the user interact directly with this resource?
* Pattern: `{UserFacing}`
* Pattern Components:
  * **UserFacing**:`([true|false]{1})` for true or false

## CritInfra

| Type         | Example Value | Required | Default Value |
| ------------ | ------------- | -------- | ------------- |
| Unsigned Int | `5`           | True     | `1`           |

* Description: What is the level of criticality of the resource? This is mesaured on a scale of 5, with 5 being the most critical.
* Pattern: `{CritInfra}`
* Pattern Components:
  * **CritInfra**: `([0-5]{1})` for the scale of criticality. How important is this resource (With 5 being critical, i.e. 24/7/365)

## SourceControl

| Type   | Example Value                                                                           | Required | Default Value      |
| ------ | --------------------------------------------------------------------------------------- | -------- | ------------------ |
| String | `https://github.com/binder-examples/continuous-build/blob/foo-G2.1_DS_art_1.0.0.tar.gz` | True     | N/A - User Defined |

* Description: This should be an URL to the source code/or documentation of the software deployed on the resource.
* Pattern: `{SourceControl}`
* Pattern Components:
  * **SourceControl**:`^((http[s]?|ftp):\/)?\/?([^:\/\s]+)((\/\w+)*\/)([\w\-\.]+[^#?\s]+)(.*)?(#[\w\-]+)?$` for identifying the source of the software deployed
* Pattern Example:
  * [https://artifactory.com/artifactory/webapp/#/artifacts/browse/tree/Properties/general/foo-G2.1\_DS\_art\_1.0.0.tar.gz](https://artifactory.com/artifactory/webapp/#/artifacts/browse/tree/Properties/general/foo-G2.1\_DS\_art\_1.0.0.tar.gz)

### Abbreviation Conventions <a href="#ac" id="ac"></a>

General baseline for abbreviations:

* Will make use of the abbreviations that have been created by AWS already
* As a general rule of thumb, if value is longer than single word, make it an abbreviation
* All abbreviations must be alphanumeric and lowercase
* Abbreviations should not exceed 6 characters

#### Tag Abbreviations <a href="#ta" id="ta"></a>

| AWS Resource Type\*          | Abbreviation |   | Capability\*\* | Abbreviation |
| ---------------------------- | ------------ | - | -------------- | ------------ |
| Application Load Balancer    | alb          |   |                |              |
| Classic Load Balancer        | elb          |   |                |              |
| Elastic Compute Cloud        | ec2          |   |                |              |
| Elastic Container Service    | ecs          |   |                |              |
| ElastiCache - Redis          | red          |   |                |              |
| ElastiCache - Memcache       | mem          |   |                |              |
| Lambda                       | lmb          |   |                |              |
| Relational Database Service  | rds          |   |                |              |
| Simple Storage Service       | s3           |   |                |              |
| Elastic Beanstalk            | eb           |   |                |              |
| Elastic File System          | efs          |   |                |              |
| Elasticsearch                | es           |   |                |              |
| Glacier                      | gla          |   |                |              |
| Identity & Access Management | iam          |   |                |              |
| API Gateway                  | api          |   |                |              |
| Autoscaling Group            | asg          |   |                |              |
| Cloudformation               | cf           |   |                |              |
| Cloudwatch                   | cw           |   |                |              |
| Elastic Block Storage        | ebs          |   |                |              |
| Route 53                     | r53          |   |                |              |
| Simple Notification Service  | sns          |   |                |              |
| Simple Queue Service         | sqs          |   |                |              |

> \* Will be using the AWS CLI [services](http://docs.aws.amazon.com/cli/latest/reference/#available-services) as reference for AWS Resource Types.

> \*\* This is not the complete list of all the available capabilities

#### S3 Region Endpoint Abbreviations <a href="#ea" id="ea"></a>

| GovCloud\*    | Abbreviation |           | Commerical Cloud | Abbreviation |
| ------------- | ------------ | --------- | ---------------- | ------------ |
| us-gov-west-1 | ugw1         | us-east-1 | ue1              |              |
|               |              |           | us-east-2        | ue2          |
|               |              |           | us-west-1        | uw1          |
|               |              |           | us-west-2        | uw2          |

> \* A second GovCloud region is in the works and is expected to be released in 2018

### Resource Specific Tags <a href="#rst" id="rst"></a>

#### SSM Parameter Store <a href="#ssmparam" id="ssmparam"></a>

The paths in SSM Parameter Store should follow the conventions and guidelines laid out in the Unity [SSM Parameters page](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/users-guide/deployment/unity-ssm-parameters).&#x20;
