# How to obtain and authenticate with your Snyk API token

Your Snyk API token is a personal token available under your user profile. A token is required to authenticate with Snyk as an individual user and is also used in `SNYK_TOKEN` parameters. The Snyk API token is associated with your Snyk Account and not with a specific Organization.

Free, Team, and Trial plan users have access only to this token under the user profile. The personal token can be used to authenticate with:

* A CI/CD integration
* The Snyk CLI running on a local or a build machine
* An IDE when setting a token manually.

Enterprise users have access to a token under their profile and to service account tokens.

* Use a service account to authenticate for any kind of automation. This includes, but is not limited to, CI/CD scanning with the CLI or build system plugins and automations, including the API.
* Enterprise users should use the token under their personal profile for:
  * Running the CLI locally on their machine
  * Authenticating the IDE manually
  * Running API calls one time, for example, to test something

For more information on the Snyk API token, see the following pages: [Authenticate the CLI with your account](../snyk-cli/authenticate-the-cli-with-your-account.md) and [Authentication for API](../snyk-api-info/authentication-for-api.md).

Follow these steps to obtain your Snyk API token:

1. Log in to Snyk and navigate to your **Account settings**
2. In the **Account Settings,** select **General** > **Auth Token**
3. Click inside the **KEY** box to display your API token.
4. Copy the token and save it in a secure location for future use.

<figure><img src="../.gitbook/assets/Snyk Broker - API Token - Account settings - API Token box.png" alt="Settings page, display API token"><figcaption><p>Settings page, display API token</p></figcaption></figure>