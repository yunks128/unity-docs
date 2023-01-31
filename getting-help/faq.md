---
description: Frequently Asked Questions
---

# FAQ

## Usage

<details>

<summary>Can I run my own algorithms on Unity?</summary>

Currently, within the Jupyter-based interface you may unit test your own algorithms. Running your algorithms at scale in our Science Processing system is a future capability being actively developed.

</details>

<details>

<summary>Where do I report a bug?</summary>

Currently, the best way to file a bug is to navigate to [https://github.com/unity-sds](https://github.com/unity-sds) and identify the repository which best pertains to your issue, and file there by clicking on the "Issues" tab.

For example, for:\
\- Analytics issues: [https://github.com/unity-sds/unity-analytics](https://github.com/unity-sds/unity-analytics)\
\- Algorithm issues: [https://github.com/unity-sds/unity-ads-deployment](https://github.com/unity-sds/unity-ads-deployment)\
\- Common service issues: [https://github.com/unity-sds/unity-cs](https://github.com/unity-sds/unity-cs)\
\- Data issues: [https://github.com/unity-sds/unity-data-services](https://github.com/unity-sds/unity-data-services)\
\- Science processing issues: [https://github.com/unity-sds/unity-sps-prototype](https://github.com/unity-sds/unity-sps-prototype), [https://github.com/unity-sds/unity-sps-workflows](https://github.com/unity-sds/unity-sps-workflows), [https://github.com/unity-sds/ades\_wpst](https://github.com/unity-sds/ades\_wpst)\
\- Documentation issues: [https://github.com/unity-sds/unity-docs](https://github.com/unity-sds/unity-docs)\
\
In the future, we aim to have a centralized ticket triaging system.



</details>

<details>

<summary>What's the best way to learn and get started with Jupyter?</summary>

Please see: [https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html](https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html)

</details>

<details>

<summary>What's an example of the type of data I can expect from a Unity user-interface?</summary>

As an example, see the below query and data returned from the "Working with Data" Jupyter notebook: \
\
Query/Action:\
\
Response:&#x20;



</details>

## Development

<details>

<summary>When should I contribute to a repository's README versus this doc site?</summary>

Think of a `README.md` as an introduction to a particular module of software and this doc site (i.e. GitBook) as an introduction to the aggregated software experience. Depending on the nature of the software repository hosting the `README.md`, whether a miscellanous tool or an all-encompassing mega-repository, you'll have to decide where to conribute. For the former case, contributing to a `README.md` is usually sufficient - especially if the tool is not core to the software experience and hasn't been described already in this central doc site. For the latter, it is preferable to contribute content (esp. user guides, developer guides, admin guides, feature list, quickstarts, FAQ, contributing guides, etc.) to our central doc site versus individual `README's`. In this case, still make sure to provide links to sections within the `README.md` to relevant areas of this doc site as needed (see [example README.md](https://github.com/unity-sds/unity-architecture#readme)).

</details>

<details>

<summary>I'm getting a parse error when trying to embed my Open API file / reference in GitBook</summary>

If you're getting an error such as "Could not parse the Open API file, it is most likely invalid" - try ensuring you are pointing to a URL that is showing the _raw_ OpenAPI document file - not a rendering. For example, pointing to a GitHub link, ensure your link is pointing to the raw version and not an HTML page (check via the page source).

</details>

## General

<details>

<summary>Where can I see a timeline of upcoming features?</summary>

TBD

</details>
