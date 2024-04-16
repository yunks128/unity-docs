# Data Services custom metadata search

## Data Service: Custom Metadata Searchability

### **Status**

What is the status, such as proposed, accepted, rejected, deprecated, superseded, etc.? Maintain the Date in this section and previous statuses as well:

| Status   | Date       |
| -------- | ---------- |
| Proposed | 10/12/2023 |
| Accepted | 11/1/2023  |

### **Context**

As we begin to populate collection specific metadata within the data catalog, users want to be able to search for this information. We have 2 realistic options for this searching- one is to update or use the DAPA items search to create queries that can search custom metadata, the other is to expose the Elastic Search directly to users for searching and filtering on whatever metadata they prefer.

### Alternatives

Option 1 - Expose Elastic Search cluster directly to user for custom metadata search

Option 2 - Utilize Common Query Language (CQL) for custom metadata filtering **(proposed solution)**

### **Decision and Rationale**

Ultimately, the decision to expose elastic search directly to the user, while preferable from a technical level and the ability to filter/aggregate searches using elastic search queries is ideal, the custom metadata and collection/item metadata are housed in multiple elastic search instances and cannot be cross queries. For now, the CQL filter will be used to hide the multi-es instances behind the scenes. We will revisit this if/when the Elastic Search database become unified.

The proposed solution also does not require exposing (and thus understanding the auth model) of ES.&#x20;

Furthermore, the ability to create STAC outputs from elasict search queries is not supported currently, and so some way of mapping the ES direct queries to elastic search qould be required to _use_ the results.

### **Impacts**

CQL will need to be documented and tested. Common filters (e.g. a single value, string, etc) might be pretty straight forward but complex metadata (e.g. nested JSON) might not be supported. This does allow us to migrate to different technologies (e.g. elastic search, databases, etc) in the future without impacting the users.

The use of CQL will require development effort within the DAPA request and we're not sure this will be supported by the process mapper functionality.&#x20;

The CQL development will also duplicate native capabilties of elastic search and this was a primary concern with the cost of development.

Lastly, the results will be returned via STAC so this should integrate with stage in requests as well where needed.

### References

(Optional) Any other references that make sense. Documentation links, other ADRs, etc.

{% embed url="https://docs.up42.com/developers/api-assets/stac-cql" %}

{% embed url="https://pystac-client.readthedocs.io/en/latest/tutorials/cql2-filter.html" %}
