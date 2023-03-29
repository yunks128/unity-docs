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

| Name                                                                                                                                                                                 | Description                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------ |
| <mark style="color:red;"></mark>[<mark style="color:red;">Name</mark>](unity-aws-resource-tagging-conventions.md#name)<mark style="color:red;"></mark>                               | Name of the resource.                                                                |
| [Venue](unity-aws-resource-tagging-conventions.md#venue)                                                                                                                             | The name of the venue that the resource is being deployed on                         |
| [Proj](unity-aws-resource-tagging-conventions.md#project)                                                                                                                            | The name of the project/mission (e.g. `europa`), or `unity` if it's a shared service |
| [ServiceArea](unity-aws-resource-tagging-conventions.md#servicearea)                                                                                                                 | Which service area is this related to?                                               |
| [Capability](unity-aws-resource-tagging-conventions.md#capability)                                                                                                                   | Name of the application                                                              |
| [CapVersion](unity-aws-resource-tagging-conventions.md#capversion)                                                                                                                   | The version number of the capability provided. Assumes semantic versioning.          |
| [Component](unity-aws-resource-tagging-conventions.md#component)                                                                                                                     | What is the primary component that makes up the application                          |
| <mark style="color:red;"></mark>[<mark style="color:red;">CreatedBy</mark>](unity-aws-resource-tagging-conventions.md#createdby)<mark style="color:red;"></mark>                     | Kion tag used for costing                                                            |
| <mark style="color:red;"></mark>[<mark style="color:red;">Customer</mark>](unity-aws-resource-tagging-conventions.md#customer)<mark style="color:red;"></mark>                       | Kion tag used for costing                                                            |
| <mark style="color:red;"></mark>[<mark style="color:red;">Env</mark>](unity-aws-resource-tagging-conventions.md#env)<mark style="color:red;"></mark>                                 | Kion tag used for costing                                                            |
| <mark style="color:red;"></mark>[<mark style="color:red;">mcpBilling</mark>](unity-aws-resource-tagging-conventions.md#mcpbilling)<mark style="color:red;"></mark>                   | Kion tag used for costing                                                            |
| <mark style="color:red;"></mark>[<mark style="color:red;">mission</mark>](unity-aws-resource-tagging-conventions.md#mission)<mark style="color:red;"></mark>                         | Kion tag used for costing                                                            |
| <mark style="color:red;"></mark>[<mark style="color:red;">ServiceNow Instance</mark>](unity-aws-resource-tagging-conventions.md#servicenow-instance)<mark style="color:red;"></mark> | Kion tag used for costing                                                            |
| <mark style="color:red;"></mark>[<mark style="color:red;">Stack</mark>](unity-aws-resource-tagging-conventions.md#stack)<mark style="color:red;"></mark>                             | Kion tag used for costing                                                            |
| <mark style="color:red;"></mark>[<mark style="color:red;">station</mark>](unity-aws-resource-tagging-conventions.md#station)<mark style="color:red;"></mark>                         | Kion tag used for costing                                                            |

### Optional  Tags <a href="#mbt" id="mbt"></a>

These tags can be used to provide additional information and context about the AWS resources.

| Name                                                                       | Description                                                                    |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| [POC](unity-aws-resource-tagging-conventions.md#poc)                       | A list of email(s) for the point of contact for the resource                   |
| [Release](unity-aws-resource-tagging-conventions.md#release)               | The version number that this resource belongs to. Assumes semantic versioning. |
| [SecurityPlanID](unity-aws-resource-tagging-conventions.md#securityplanid) | IT Security Plan ID for the resource                                           |
| [ExposedWeb](unity-aws-resource-tagging-conventions.md#exposedweb)         | Will this resource be exposed to the web?                                      |
| [Experimental](unity-aws-resource-tagging-conventions.md#experimental)     | Is this an experimental resource?                                              |
| [UserFacing](unity-aws-resource-tagging-conventions.md#userfacing)         | Will this resource be user facing?                                             |
| [CritInfra](unity-aws-resource-tagging-conventions.md#critinfra)           | Is this resource a part of the critical infrastructure?                        |
| [SourceControl](unity-aws-resource-tagging-conventions.md#sourcecontrol)   | Documentation or SCM link for resources deployed                               |

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

| Type   | Example Value      | Required | Default Value      |
| ------ | ------------------ | -------- | ------------------ |
| String | `foo@jpl.nasa.gov` | True     | N/A - User Defined |

* Description: The email of the creator of the AWS resource.
* Pattern: `{Email}`
* Pattern Components:
  * **Email** :`(^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$)` for the email of the creator of the resource.
* Pattern Example:
  * `foo@jpl.nasa.gov`
* `Category: Technical, Costing`

## Customer

| Type   | Example Value        | Required | Default Value      |
| ------ | -------------------- | -------- | ------------------ |
| String | `Sounder SIPS Admin` | True     | N/A - User Defined |

* Description: The customer (who uses it) for the AWS resource
* Example:  <mark style="color:red;">TODO:</mark> [Galen Hollins](http://localhost:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
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

* Description: <mark style="color:red;">TODO:</mark> [Galen Hollins](http://localhost:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
* Example:  <mark style="color:red;">TODO:</mark> [Galen Hollins](http://localhost:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
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
* Example:  <mark style="color:red;">TODO:</mark> [Galen Hollins](http://localhost:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
* Description: <mark style="color:red;">TODO:</mark> [Galen Hollins](http://localhost:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>

## Stack

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `U-AS`        | True     | N/A - User Defined |

* `Category: Technical, Costing`
* Example:  <mark style="color:red;">TODO:</mark> [Galen Hollins](http://localhost:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
* Description: <mark style="color:red;">TODO:</mark> [Galen Hollins](http://localhost:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>

## station

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `tbd`         | True     | N/A - User Defined |

* `Category: Technical, Costing`
* Example:  <mark style="color:red;">TODO:</mark> [Galen Hollins](http://localhost:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>
* Description: <mark style="color:red;">TODO:</mark> [Galen Hollins](http://localhost:5000/u/bzYDKXoPxld10sWkbG1EP6O4TUX2 "mention")<mark style="color:red;">provide more description and examples here</mark>



## POC

| Type   | Example Value(s)                           | Required | Default Value      |
| ------ | ------------------------------------------ | -------- | ------------------ |
| String | `foo-sps@jpl.nasa.gov:foo-ds@jpl.nasa.gov` | True     | N/A - User Defined |

* Description: The list of the point of contacts that is responsible for the resource is being deployed on.
* Pattern: `{Poc}`
* Pattern Components:
  * **Poc**:`([a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+)` for the email of the creator of the resource.

## Venue

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `dev`         | True     | N/A - User Defined |

* Description: The name of the venue that the resource is being deployed on.
* Pattern: `{Venue}`
* Pattern Components:
  * **Venue**:`(dev|test|prod)` for development, test, production

## Proj

| Type   | Example Values    | Required | Default Value | Category |
| ------ | ----------------- | -------- | ------------- | -------- |
| String | `europa`, `unity` | True     | `foo`         | Business |

* Description: The name of the project/mission (e.g. `europa`), or `unity` if it's a shared service
* Pattern: `{Project}`
* Pattern Components:
  * **Project**:`(foo)` for the shorthand project abbreviation
* Category: Technical, Costing

## ServiceArea

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `sps`         | True     | N/A - User Defined |

* Description: The name of the Unity service area.
* Pattern: `{ServiceArea}`
* Pattern Components:
  * **ServiceArea**:`(cs\|sps\|ds\|ads\|as)`|See `ServiceArea` for more info. The service area abbreviation, or "all" if the resource is to be used by all service areas
* Pattern Example:
  * `sps`

## Capability

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `hysds`       | True     | N/A - User Defined |

* Description: Name of the application.
* Pattern: `{Capability}`
* Pattern Components:
  * **Capability**:`([a-z0-9]+)` for the capability that is provided by the service area.

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

| Type   | Example Value | Required | Default Value      |
| ------ | ------------- | -------- | ------------------ |
| String | `java`        | True     | N/A - User Defined |

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

Resource specific tags takes precedence over the mandatory and optional baseline tags. Meaning that if the the resource specific tag's key name is the same as the baseline, follow the resource-specific convention.

* EC2
* Amazon S3
* ALBs, Target Groups, and Redis
* SSM Parameter Store
* KMS Encryption Key

#### EC2 <a href="#ec2" id="ec2"></a>

| Tag       | Description          | Type   | Pattern Example | Required | Default Value      | Category  |
| --------- | -------------------- | ------ | --------------- | -------- | ------------------ | --------- |
| EbsBackup | EBS Backup Frequency | String | `0800;6;all`    | True     | N/A - User Defined | Technical |

* _**EbsBackup**_
  * **Description:** The EBS snapshot scheduler event pattern for EC2
  * **Pattern:** `{snapshot_time};{retention_days};{active_day}>`
  * **Pattern Components**
    * See TBD... for more information

#### Amazon S3 <a href="#s3" id="s3"></a>

| Tag  | Description            | Type   | Pattern Example              | Required | Default Value      | Category  |
| ---- | ---------------------- | ------ | ---------------------------- | -------- | ------------------ | --------- |
| Name | Name of the AWS Bucket | String | `foo-tb-sps-tet-ingest-logs` | True     | N/A - User Defined | Technical |

* _**Name**_
  * **Description:** The bucket name must be unique across all existing buckets names in Amazon S3.
  * **Pattern:** `{Proj}-{Venue}-{ServiceArea}-{Capability}-{Component or other identifiers}`
  *   **Pattern Components**

      | Component   | Regex Pattern            | Description                                                                                                                            |
      | ----------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
      | Proj        | `(foo)`                  | Shorthand project abbreviation                                                                                                         |
      | Venue       | `([a-z0-9]+)`            | See `Venue` for more info.                                                                                                             |
      | ServiceArea | `(cs\|sps\|ds\|ads\|as)` | See `ServiceArea` for more info.                                                                                                       |
      | Capability  | `([a-z0-9]+)`            | For the name of the application. See `Capability` for more info.                                                                       |
      | Component   | `([a-z0-9\-]+)`          | for the component that the application falls under. e.g. (backend) for backend, (ft) for frontend, etc. See `Component` for more info. |

| Tag        | Description                                                                                                                      | Type   | Pattern Example | Required | Default Value | Category            |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------- | ------ | --------------- | -------- | ------------- | ------------------- |
| ExportComp | Compliant to US Export control regulations. Does/Will this bucket contain data that subject under US Export control regulations. | String | ITAR            | True     | None          | Technical, Security |

* _**ExportComp**_
  * **Description**: Determine if the data stored on the bucket will contain EAR or ITAR data.
  * **Pattern**: `{[EAR|ITAR|None]}`

| Tag         | Description                                                        | Type   | Pattern Example | Required | Default Value      | Category  |
| ----------- | ------------------------------------------------------------------ | ------ | --------------- | -------- | ------------------ | --------- |
| DataProfile | What type of data is being stored in this bucket. (i.e. RML, JSON) | String | RML:JSON        | False    | N/A - User Defined | Technical |

* _**DataProfile**_
  * **Description**: What type of data will be stored on this bucket?
  * **Pattern**: `{[a-z0-9:]+}`

#### ALBs, Target Groups, and Redis (Short name version) <a href="#shortname" id="shortname"></a>

| Tag  | Description          | Type   | Pattern Example | Required | Default Value      | Category  |
| ---- | -------------------- | ------ | --------------- | -------- | ------------------ | --------- |
| Name | Name of the resource | String | `dev-hysds-00`  | True     | N/A - User Defined | Technical |

* _**Name**_
  * **Description:** The short name of the AWS resource. All characters need to be in lowercase. The short version is used to name resources with character limit restrictions.
  * **Pattern:** `{Venue}-{Capability}-{Id}`
  *   **Pattern Components**

      | Component  | Regex Pattern      | Description                                                                                                      |
      | ---------- | ------------------ | ---------------------------------------------------------------------------------------------------------------- |
      | Venue      | `([a-z0-9]{1,6})`  | Limit 6 characters. Examples include dev, test, and prod.                                                        |
      | Capability | `([a-z0-9]{1,10})` | Name of the application. Truncates to 10 characters when using the short pattern. Should be unique project wide. |
      | Id         | `([a-z0-9]{1,2})`  | Unique identifier.                                                                                               |
  * **Pattern Example**:
    * `train1-rsvp-1`
    * `dev-hysds-5f`

#### SSM Parameter Store <a href="#ssmparam" id="ssmparam"></a>

| Tag  | Description          | Type   | Pattern Example                                  | Required | Default Value      | Category  |
| ---- | -------------------- | ------ | ------------------------------------------------ | -------- | ------------------ | --------- |
| Name | Name of the resource | String | `/foo/all/sps/artifactory/_vars/ARTIFACTORY_URL` | True     | N/A - User Defined | Technical |

* _**Name**_
  * **Description:** A path-based name for SSM Parameters. Path-based naming is required because the AWS API allows for path-based lookups of Parameters
  * **Pattern:** `/{Proj}/{Venue}/{ServiceArea}/{Capability}/{ParamName}`
  *   **Pattern Components**

      | Component   | Regex Pattern            | Description                                                                                                                 |
      | ----------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
      | Proj        | `(foo)`                  | Project abbreviation                                                                                                        |
      | Venue       | `([a-z0-9])`             | The venue. Examples include dev, test, and prod.                                                                            |
      | ServiceArea | `(cs\|sps\|ds\|ads\|as)` | See `ServiceArea` for more info. The service area abbreviation, or "all" if the resource is to be used by all service areas |
      | Capability  | `([a-z0-9])`             | Name of the application. Truncates to 10 characters when using the short pattern. Should be unique project wide.            |
      | ParamName   | `([A-Z0-9])`             | The name of the parameter. All caps, alphanumeric + underscores only                                                        |
  * **Pattern Example**:
    * `/foo/all/sps/artifactory/_vars/ARTIFACTORY_URL`
    * `/foo/test/sps/hysds/_secrets/DB_PASSWORD`

#### KMS Encryption Key <a href="#kmskey" id="kmskey"></a>

| Tag  | Description          | Type   | Pattern Example                      | Required | Default Value      | Category  |
| ---- | -------------------- | ------ | ------------------------------------ | -------- | ------------------ | --------- |
| Name | Name of the resource | String | `ec2-param/foo/dev/sps/frontend/env` | True     | N/A - User Defined | Technical |

* _**Name**_
  * **Description:** A path-based alias for KMS encryption keys. Path-based naming is not required for functional reasons, but is used for readability because the primary use of KMS encryption keys is to encrypt SSM Parameters, and SSM Parameters use path-based names.
  * **Pattern:** `{Resource}/{Proj}/{Venue}/{ServiceArea}/{Capability}/{Component}`
  *   **Pattern Components**

      | Component   | Regex Pattern            | Description                                                                                                                                                                                             |
      | ----------- | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
      | Resource    | `([a-z0-9)`              | If the encryption key will be used to encrypt an AWS resource (e.g. an SSM Parameter), this prefix designates the resource type of the target (e.g. ssm-param for keys used to encrypt SSM Parameters). |
      | Proj        | `(foo)`                  | Project abbreviation                                                                                                                                                                                    |
      | Venue       | `([a-z0-9])`             | The venue. Examples include dev, test, and prod.                                                                                                                                                        |
      | ServiceArea | `(cs\|sps\|ds\|ads\|as)` | See `ServiceArea` for more info. The service area abbreviation, or "all" if the resource is to be used by all service areas                                                                             |
      | Capability  | `([a-z0-9])`             | Name of the application. Truncates to 10 characters when using the short pattern. Should be unique project wide.                                                                                        |
      | Component   | `([a-z0-9]\|env)`        | The component to be encrypted. `env` designates the key as an environmental encryption key to be used by multiple resources                                                                             |
  * **Pattern Example**:
    * `ssm-param/foo/all/sps/artifactory/env`
    * `ssm-param/foo/all/env`
