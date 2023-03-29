# Unity AWS Resource Tagging Conventions



#### WORK IN PROGRESS (DON'T USE FOR OFFICIAL PURPOSES YET!)

**Version 1**

### Introduction

Amazon Web Services provides the ability to assign metadata to AWS resources in the form of **tags**. Unity has compiled a list of tags that we believe will be needed to allow the team to easily categorize resources by purpose, owner, environment, and other specific criteria.  **Also, importantly, these tags allow for tracking of how much money is spent on the various AWS resources.**  This document describes Unity tagging convention and pattern for various projects.

This is a living document, and may be subjected to change as requirements may change during the developmental cycle.

### Table of Contents

1. General Guidelines
2. Tagging Categories
3. Mandatory Baseline Tags
4. Abbreviation Conventions
   * Tag Abbreviation
   * S3 Region Endpoint Abbreviations
5. Resource Specific Tags
   * Amazon S3
   * ALBs, Target Groups, and Redis (Short name version)
   * SSM Parameter Store
   * KMS Encryption Key

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
* **Business**: To help associate AWS costs to the users of the resource
* **Security**: For determining security configuration and compliance of AWS resources

### Mandatory Baseline Tags <a href="#mbt" id="mbt"></a>

These tags should be provided in all AWS resources.

| Name                                                | Description                                                                 |
| --------------------------------------------------- | --------------------------------------------------------------------------- |
| <mark style="color:red;">Name</mark>                | Name of the resource.                                                       |
| Venue                                               | The name of the venue that the resource is being deployed on                |
| Project                                             | The name of the project                                                     |
| ServiceArea                                         | Which service area is this related to?                                      |
| Capability                                          | Name of the application                                                     |
| CapVersion                                          | The version number of the capability provided. Assumes semantic versioning. |
| Component                                           | What is the primary component that makes up the application                 |
| <mark style="color:red;">CreatedBy</mark>           | Kion tag used for costing                                                   |
| <mark style="color:red;">Customer</mark>            | Kion tag used for costing                                                   |
| <mark style="color:red;">Env</mark>                 | Kion tag used for costing                                                   |
| <mark style="color:red;">mcpBilling</mark>          | Kion tag used for costing                                                   |
| <mark style="color:red;">mission</mark>             | Kion tag used for costing                                                   |
| <mark style="color:red;">Proj</mark>                | Kion tag used for costing                                                   |
| <mark style="color:red;">ServiceNow Instance</mark> | Kion tag used for costing                                                   |
| <mark style="color:red;">Stack</mark>               | Kion tag used for costing                                                   |
| <mark style="color:red;">station</mark>             | Kion tag used for costing                                                   |

### Optional Baseline Tags <a href="#mbt" id="mbt"></a>

These tags can be used to provide additional information and context about the AWS resources.

| Name           | Description                                                                    |
| -------------- | ------------------------------------------------------------------------------ |
| POC            | A list of email(s) for the point of contact for the resource                   |
| Venue          | The name of the venue that the resource is being deployed on                   |
| Project        | The name of the project                                                        |
| Release        | The version number that this resource belongs to. Assumes semantic versioning. |
| SecurityPlanID | IT Security Plan ID for the resource                                           |
| ExposedWeb     | Will this resource be exposed to the web?                                      |
| Experimental   | Is this an experimental resource?                                              |
| UserFacing     | Will this resource be user facing?                                             |
| CritInfra      | Is this resource a part of the critical infrastructure?                        |
| SourceControl  | Documentation or SCM link for resources deployed                               |



> See below for the required tag specifications.
>
> CreatedBy Customer Env mcpBilling mission Name Proj ServiceNow Instance Stack station

***

#### Baseline Tags Specifications

| Tag      | Description          | Type   | Pattern Example           | Required | Default Value      | Category  |
| -------- | -------------------- | ------ | ------------------------- | -------- | ------------------ | --------- |
| **Name** | Name of the resource | String | `foo-dev-sps-hysds-mid01` | True     | N/A - User Defined | Technical |

* Description: The name of the AWS resource. All characters need to be in lowercase.
* Pattern: `{Project}-{Venue}-{ServiceArea}-{Capability}-{Component}`
* Pattern Components:
  * **Proj** :`(foo)` for the project abbreviation.
  * **Venue** :`([a-z0-9]{1,6})` See `Venue` below for more info. Limit 6 characters. Examples include dev, test, and prod.
  * **ServiceArea**:`(cs\|sps\|ds\|ads\|as)` See `ServiceArea` for more info. The service area abbreviation, or "all" if the resource is to be used by all service areas
  * **Capability**: `([a-z0-9]+)` for the name of the application. See `Capability` below for more info. Should be unique project wide.
  * **Component**: `([a-z0-9]+)` for the component that the application falls under. e.g. (backend) for backend, (ft) for frontend, etc. See `Component` below for more info.
* Pattern Example:
  * `foo-dev-sps-hysds-mid01`
  * `foo-train1-rps-rsvp-ft01`

| Tag           | Description                          | Type   | Pattern Example    | Required | Default Value      | Category |
| ------------- | ------------------------------------ | ------ | ------------------ | -------- | ------------------ | -------- |
| **CreatedBy** | Email of the creator of the resource | String | `foo@jpl.nasa.gov` | True     | N/A - User Defined | Business |

* Description: The email of the creator of the AWS resource.
* Pattern: `{Email}`
* Pattern Components:
  * **Email** :`(^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$)` for the email of the creator of the resource.
* Pattern Example:
  * `foo@jpl.nasa.gov`

| Tag     | Description                                                   | Type   | Pattern Example                            | Required | Default Value      | Category |
| ------- | ------------------------------------------------------------- | ------ | ------------------------------------------ | -------- | ------------------ | -------- |
| **POC** | A list of email(s) for the point of contact for the resource. | String | `foo-sps@jpl.nasa.gov:foo-ds@jpl.nasa.gov` | True     | N/A - User Defined | Business |

* Description: The list of the point of contacts that is responsible for the resource is being deployed on.
* Pattern: `{Poc}`
* Pattern Components:
  * **Poc**:`([a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+)` for the email of the creator of the resource.
* Pattern Example:
  * `foo@jpl.nasa.gov`

| Tag       | Description                                                   | Type   | Pattern Example | Required | Default Value      | Category  |
| --------- | ------------------------------------------------------------- | ------ | --------------- | -------- | ------------------ | --------- |
| **Venue** | The name of the venue that the resource is being deployed on. | String | `dev`           | True     | N/A - User Defined | Technical |

* Description: The name of the venue that the resource is being deployed on.
* Pattern: `{Venue}`
* Pattern Components:
  * **Venue**:`(dev|test|prod)` for development, test, production
* Pattern Example:
  * `dev`

| Tag         | Description             | Type   | Pattern Example | Required | Default Value | Category |
| ----------- | ----------------------- | ------ | --------------- | -------- | ------------- | -------- |
| **Project** | The name of the project | String | `foo`           | True     | `foo`         | Business |

* Description: The name of the project (Flight mission).
* Pattern: `{Project}`
* Pattern Components:
  * **Project**:`(foo)` for the shorthand project abbreviation
* Pattern Example:
  * `foo`

| Tag             | Description                        | Type   | Pattern Example | Required | Default Value      | Category |
| --------------- | ---------------------------------- | ------ | --------------- | -------- | ------------------ | -------- |
| **ServiceArea** | The name of the Unity service area | String | `sps`           | True     | N/A - User Defined | Business |

* Description: The name of the Unity service area.
* Pattern: `{ServiceArea}`
* Pattern Components:
  * **ServiceArea**:`(cs\|sps\|ds\|ads\|as)`|See `ServiceArea` for more info. The service area abbreviation, or "all" if the resource is to be used by all service areas
* Pattern Example:
  * `sps`

| Tag            | Description             | Type   | Pattern Example | Required | Default Value      | Category  |
| -------------- | ----------------------- | ------ | --------------- | -------- | ------------------ | --------- |
| **Capability** | Name of the application | String | `hysds`         | True     | N/A - User Defined | Technical |

* Description: Name of the application.
* Pattern: `{Capability}`
* Pattern Components:
  * **Capability**:`([a-z0-9]+)` for the capability that is provided by the service area.
* Pattern Example:
  * `hysds`
  * `tenantManager`

| Tag            | Description                                                                 | Type   | Pattern Example | Required | Default Value      | Category  |
| -------------- | --------------------------------------------------------------------------- | ------ | --------------- | -------- | ------------------ | --------- |
| **CapVersion** | The version number of the capability provided. Assumes semantic versioning. | String | `1.0.1`         | True     | N/A - User Defined | Technical |

* Description: Version of the application.
* Pattern: `{CapVersion}`
* Pattern Components:
  * **CapVersion**:`^(\d+\.)?(\d+\.)?(\*|\d+)$` for the capability version that is provided by the service area.
* Pattern Example:
  * `1.0.0`
  * `0.0.1`
  * `1.23.1`

| Tag         | Description                                                                            | Type   | Pattern Example | Required | Default Value      | Category  |
| ----------- | -------------------------------------------------------------------------------------- | ------ | --------------- | -------- | ------------------ | --------- |
| **Release** | The release version number that this resource belongs to. Assumes semantic versioning. | String | `G3.0`          | True     | N/A - User Defined | Technical |

* Description: Release version that the application belongs to.
* Pattern: `{Release}`
* Pattern Components:
  * **Release**:`^G(\d+\.)?(\d+\.)?(\*|\d+)$` for the release version that is provided by the service area.
* Pattern Example:
  * `G1.0`
  * `G2.1`
  * `G3.0.1`

| Tag           | Description                                                 | Type   | Pattern Example | Required | Default Value      | Category  |
| ------------- | ----------------------------------------------------------- | ------ | --------------- | -------- | ------------------ | --------- |
| **Component** | What is the primary component that makes up the application | String | `java`          | True     | N/A - User Defined | Technical |

* Description: The primary type of application/runtime that will be run on this resource.
* Pattern: `{Component}`
* Pattern Components:
  * **Component**:`([a-z0-9]+)` for the primary type of application/runtime that run or support for this resource.
* Pattern Example:
  * `nodejs`
  * `mysql`
  * `tomcat`

| Tag                | Description                          | Type         | Pattern Example | Required | Default Value | Category |
| ------------------ | ------------------------------------ | ------------ | --------------- | -------- | ------------- | -------- |
| **SecurityPlanID** | IT Security Plan ID for the resource | Unsigned Int | `644`           | True     | `644`         | Security |

* Description: The JPL security plan ID that this resource falls under.
* Pattern: `{PlanId}`
* Pattern Components:
  * **PlanId**:`([0-9]+)` for JPL security plan ID for this resource.
* Pattern Example:
  * `644`

| Tag            | Description                               | Type    | Pattern Example | Required | Default Value | Category |
| -------------- | ----------------------------------------- | ------- | --------------- | -------- | ------------- | -------- |
| **ExposedWeb** | Will this resource be exposed to the web? | Boolean | `false`         | True     | `false`       | Security |

* Description: Is this resource exposed to the web?
* Pattern: `{ExposedWeb}`
* Pattern Components:
  * **ExposedWeb**:`([true|false]{1})` for true or false
* Pattern Example:
  * `false`

| Tag              | Description                       | Type    | Pattern Example | Required | Default Value | Category              |
| ---------------- | --------------------------------- | ------- | --------------- | -------- | ------------- | --------------------- |
| **Experimental** | Is this an experimental resource? | Boolean | `false`         | True     | `false`       | Automation, Technical |

* Description: Is this an experimental resource? If so, it will be removed after a period of time.
* Pattern: `{Experimental}`
* Pattern Components:
  * **Experiment**:`([true|false]{1})` for true or false
* Pattern Example:
  * `false`

| Tag            | Description                        | Type    | Pattern Example | Required | Default Value | Category |
| -------------- | ---------------------------------- | ------- | --------------- | -------- | ------------- | -------- |
| **UserFacing** | Will this resource be user facing? | Boolean | `false`         | True     | `false`       | Security |

* Description: Is this resource user facing? Does the user interact directly with this resource?
* Pattern: `{UserFacing}`
* Pattern Components:
  * **UserFacing**:`([true|false]{1})` for true or false
* Pattern Example:
  * `false`

| Tag           | Description                                             | Type         | Pattern Example | Required | Default Value | Category              |
| ------------- | ------------------------------------------------------- | ------------ | --------------- | -------- | ------------- | --------------------- |
| **CritInfra** | Is this resource a part of the critical infrastructure? | Unsigned Int | `5`             | True     | `1`           | Automation, Technical |

* **CritInfra**
  * Description: What is the level of criticality of the resource? This is mesaured on a scale of 5, with 5 being the most critical.
  * Pattern: `{CritInfra}`
  * Pattern Components:
    * **CritInfra**: `([0-5]{1})` for the scale of criticality. How important is this resource (With 5 being critical, i.e. 24/7/365)
  * Pattern Example:
    * `5`

| Tag               | Description                                      | Type   | Pattern Example                                                                         | Required | Default Value      | Category  |
| ----------------- | ------------------------------------------------ | ------ | --------------------------------------------------------------------------------------- | -------- | ------------------ | --------- |
| **SourceControl** | Documentation or SCM link for resources deployed | String | `https://github.com/binder-examples/continuous-build/blob/foo-G2.1_DS_art_1.0.0.tar.gz` | True     | N/A - User Defined | Technical |

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
