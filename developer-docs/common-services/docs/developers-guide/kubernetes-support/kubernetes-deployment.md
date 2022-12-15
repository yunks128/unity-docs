# Kubernetes Deployment

## Standalone

To deploy a standalone Kubernetes cluster you can use the deploy eks Github action. There are different actions depending on which account you want to deploy into. [Here is the link to Dev.](https://github.com/unity-sds/unity-cs-infra/actions/workflows/deploy\_eks\_oidc.yml)

<figure><img src="../../../../../.gitbook/assets/Screenshot 2022-12-12 165847.png" alt=""><figcaption><p>Github Action Variables</p></figcaption></figure>

The available inputs may change over time but currently they look like the above image. Give the cluster a name, pick something unique. Select the node sizes you want and the instance type and press the Run workflow button. It will take about 20 minutes to run, you can monitor progress via the Action console.

## **Unity Metadata**

Coming soon....
