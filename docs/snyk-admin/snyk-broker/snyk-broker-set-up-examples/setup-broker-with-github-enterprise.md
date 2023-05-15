# Set up Snyk Broker with GitHub Enterprise

Follow the instructions on this page to set up GitHub Enterprise with Snyk Broker. This integration is useful to ensure a secure connection with your on-premise or cloud GitHub Enterprise deployment.

{% hint style="info" %}
**Prerequisites**\
Ask your Snyk account team to provide you with a Broker token.

You need Docker or a way to run Docker Linux containers. Some Docker deployments for Windows run only Windows containers. Ensure that your deployment is capable of running Linux containers.
{% endhint %}

## Configure Broker to be used for GitHub Enterprise

To use the Snyk Broker client with a GitHub Enterprise deployment, **run** `docker pull snyk/broker:github-enterprise`. The following environment variables are required to configure the Broker client:

* `BROKER_TOKEN` - the Snyk Broker token, obtained from your Snyk Org settings view (app.snyk.io).
* `GITHUB_TOKEN` - a personal access token with full `repo`, `read:org` and `admin:repo_hook` scopes.
* `GITHUB` - the hostname of your GitHub Enterprise deployment, such as `your.ghe.domain.com`.
* `GITHUB_API` - the API endpoint of your GitHub Enterprise deployment. Should be `your.ghe.domain.com/api/v3`.
* `GITHUB_GRAPHQL` - the graphql endpoint of your GitHub Enterprise deployment. Should be `your.ghe.domain.com/api`.
* `PORT` - the local port at which the Broker client accepts connections. Default is 8000.
* `BROKER_CLIENT_URL` - the full URL of the Broker client as it will be accessible to your GitHub Enterprise deployment webhooks, such as `http://broker.url.example:8000`
  * This must have http:// and the port number.&#x20;
  * To configure the client with HTTPS, [additional settings are required](https://docs.snyk.io/snyk-admin/snyk-broker/install-and-configure-broker-using-docker/advanced-configuration-for-snyk-broker-docker-installation/https-for-broker-client-with-docker).
* `ACCEPT_IAC` - by default, some file types used by Infrastructure-as-Code (IaC) are not enabled. To grant the Broker access to IaC files in your repository, such as Terraform for example, you can simply add an environment variable `ACCEPT_IAC` with any combination of `tf,yaml,yml,json,tpl`
* `ACCEPT_CODE` - by default, when using the Snyk Broker - Code Agent, Snyk Code will not load code snippets. To enable code snippets you can simply add an environment variable `ACCEPT_CODE=true`

**If necessary,** go to the [Advanced configuration page](../install-and-configure-broker-using-docker/advanced-configuration-for-snyk-broker-docker-installation/) and **make any configuration changes** needed, such as providing the CA (Certificate Authority) to the Broker Client configuration if the GitHub Enterprise instance is using a private certificate, and setting up [proxy support](https://docs.snyk.io/integrations/snyk-broker/set-up-snyk-broker/how-to-install-and-configure-your-snyk-broker-client#proxy-support). See also [Adding custom accept.json for Docker installation](../install-and-configure-broker-using-docker/advanced-configuration-for-snyk-broker-docker-installation/adding-custom-allowlist-for-docker-installation.md).

## Docker run command to set up a Broker Client for GitHub Enterprise

**Copy the following command** to set up up a fully configured Broker Client to analyze Open Source, IaC, Container, and Code files (with the Code Agent).

```bash
docker run --restart=always \
           -p 8000:8000 \
           -e BROKER_TOKEN=<secret-broker-token> \
           -e GITHUB_TOKEN=<secret-github-token> \
           -e GITHUB=<your.ghe.domain.com (no http/s)> \
           -e GITHUB_API=<your.ghe.domain.com/api/v3 (no http/s)> \
           -e GITHUB_GRAPHQL=<your.ghe.domain.com/api (no http/s)> \
           -e PORT=8000 \
           -e BROKER_CLIENT_URL=<http://broker.url.example:8000 (dns/IP:port)> \
           -e ACCEPT_IAC=tf,yaml,yml,json,tpl \
           -e ACCEPT_CODE=true \
       snyk/broker:github-enterprise
```

As an alternative to using the Docker run command, you can use a derived Docker image to set up the Broker Client integration. See [Derived Docker images](derived-docker-images-for-broker-client-integrations-and-container-registry-agent.md) for the environment variables to override for the GitHub Enterprise integration.

## Start the Broker Client container and verify the connection with GitHub Enterprise

Paste the Broker Client configuration to start the Broker Client container.

Once the container is up, the GitHub Enterprise Integrations page shows the connection to GitHub Enterprise and you can `Add Projects.`

## Basic troubleshooting for Broker with GitHub Enterprise

### **Support of big manifest files (> 1Mb) for GitHub Enterprise**

One reason that open Fix/Upgrade PRs or PR/recurring tests fail may be fetching big manifest files (> 1Mb). To address this issue, whitelist an additional Blob API endpoint in `accept.json`. This should be in a private array.

```
{
    "//": "used to get given manifest file",
    "method": "GET",
    "path": "/repos/:owner/:repo/git/blobs/:sha",
    "origin": "https://${GITHUB_TOKEN}@${GITHUB_API}"
}
```

{% hint style="info" %}
To ensure the maximum possible security, Snyk does not enable this rule by default, as use of this endpoint means that the Snyk platform can theoretically access all files in this repository, because the path does not include specific allowed file names.
{% endhint %}

### **Additional troubleshooting for Broker with GitHub Enterprise**

* Run `docker logs <container id>` to look for any errors, where container id is the GitHub Enterprise Broker container ID.
* Ensure relevant ports are exposed to GitHub Enterprise.