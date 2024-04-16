# Project Management using Github Projects

We will use Github Projects to track narratives, epics, and work tickets. Each service area will have their own project board, and there will be a single project level board for all narratives.

## Narratives Board

https://github.com/orgs/unity-sds/projects/3/views/1

The narratives board holds all of the narratives for each service area. A narrative can be repeated/duplicated if it is owned by multiple service areas. Narratives should have a a link to the features required to complete it in their description. These can live in different repositories.

**Required Fields:**

* Title - the text of the narrative
* Team - owner of the narrative
* Labels - needs to contain narrative
* Narrative - the identifier of the narrative, which includes the year and narrative ID (e.g. 2023.01)
* Features Created - yes/no. Only mark yes if the underlying service area features have been created.

## Service Area Boards

Each service area has 2 required views. More views can be added by the projects as they see fit.

* Features - the features this service area is implementing
* Features Work Breakdown - The Feature + work ticket required to implement the feature.

### Features View

The features view lists all features that the service area is to implement. These are all "epics" and as such, must exist as real issues and have the 'Epic' label added to them. Each feature needs a "release", this is the program increment in which it will be implemented and delivered to the operational system. For example, a feature with release in 23.2 will be released in the 2nd increment of 2023 (April - June timeframe). Each Feature needs to have a text based epic identifier which will be used to group work needed to be completed to complete an epic.

**Features Fields**

* Title - the feature description as a user story
* Narrative - the narrative ID to which this feature belongs
* Release - the release in which this feature is planned to be delivered
* EPIC ID - the text short name for this feature. Work tickets will use this in order to group the feature with the work required to do them.

### Features Work Breakdown

The purpose of the Features Work Breakdown view is to group the individual efforts required to deliver a functionality. For example, a feature might be "implement a shopping cart". this would consist of individual tickets for things like `viewing the cart`, `purchasing the cart`, `adding items to a cart`, `removing items from the cart`, and so on.

The way this grouping is done is by assigning a feature an `EPIC ID` and then using that same `EPIC ID` in each work ticket. this allows us to see work required to complete a feature.

**Features Work Breakdown Fields**

* Title - the title of the feature or work item, usually written as a user story.
* EPIC ID - the text short name for this feature. Work tickets will use this in order to group the feature with the work required to do them.
* Narrative - the narrative ID to which this feature belongs
