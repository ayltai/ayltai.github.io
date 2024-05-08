---
layout     : post
author     : Alan Tai
date       : 2024-05-08
title      : "Securing CI/CD pipelines with OIDC, GitHub Actions, and Terraform"
image      : posts/2024/05/08/red-padlock-on-black-computer-keyboard.jpg
categories : Security, Terraform, AWS, GitHub
---
In regulated industries, where trust is paramount, the security of CI/CD pipelines is a non-negotiable priority. [Breaches within these pipelines](https://www.reversinglabs.com/blog/ci/cd-security-breaches-update-software-security-approach) can expose sensitive customer data and disrupt critical services, leading to devastating consequences. Traditional methods of storing long-lived credentials directly within the pipeline configuration create a vulnerability that erodes this trust. [OpenID Connect (OIDC)](https://openid.net/developers/how-connect-works/) offers a robust solution for secure authentication, and this article explores its implementation within a GitHub workflow using Terraform for provisioning resources on AWS.

## The inherent risks of hardcoded credentials

Traditionally, CI/CD pipelines relied on storing long-lived credentials, such as API keys and passwords, directly within the pipeline configuration. This approach introduces several security risks that threaten the integrity of regulated institutions:

* **Exposure through leaks:** Accidental leaks or compromised CI/CD systems can expose these credentials, granting unauthorised access to critical infrastructure and sensitive data.
* **Static targets:** Static credentials remain vulnerable for extended periods, as rotating them regularly becomes a logistical challenge.

## OIDC: A dynamic and secure authentication solution

OIDC is an open standard for secure authentication that eliminates the need for storing long-lived credentials within the pipeline, significantly enhancing security. Here’s how it works:

1. **Leveraging GitHub as the Identity Provider (IdP):** GitHub acts as the IdP, authenticating users and issuing short-lived JSON Web Tokens (JWTs) upon successful login.
2. **Terraform provisioning AWS resources:** Terraform code defines the infrastructure on AWS, including the GitHub IdP and an IAM role that trusts that IdP.
3. **GitHub workflow requesting OIDC token:** The GitHub workflow initiates a step that retrieves a JWT using OIDC.
4. **AWS IAM verification:** The AWS service targeted by the pipeline validates the JWT with GitHub IdP, granting temporary access based on the permissions associated with the user or role used for authentication.

This approach offers several security benefits that directly address the concerns of regulated industries:

* **Short-lived credentials:** JWTs expire quickly, minimising the window of vulnerability if compromised.
* **Reduced attack surface:** Eliminating stored credentials minimises the potential impact of a security breach.
* **Streamlined workflows:** Short-lived credentials eliminate the need for manual rotation, streamlining pipeline execution and improving development agility.
* **Improved auditability:** OIDC provides a clear audit trail for access requests, facilitating compliance efforts.

## Implementing OIDC with Terraform

Here’s a breakdown of the implementation steps.

#### Configure AWS OIDC provider

Create an OIDC provider within AWS IAM that trusts the GitHub IdP:

```java
resource "aws_iam_openid_connect_provider" "this" {
  url = "https://token.actions.githubusercontent.com"

  client_id_list = [
    "sts.amazonaws.com",
  ]

  thumbprint_list = [
    "6938fd4d98bab03faadb97b34396831e3780aea1",
  ]
}
```

#### Define IAM role with AssumeRole policy

Specify the IAM role for the pipeline, granting access to the required AWS resources and including a policy that trusts the GitHub IdP:

```java
locals {
  repositories = [
    "YourUsernameOrOrgName/RepoName", // Change the repository name
  ]

  assume_role_attach_policies = [
    "arn:aws:iam::aws:policy/AdministratorAccess", // Change the policy to fit your purpose
  ]
}

data "aws_iam_policy_document" "this" {
  statement {
    actions = [
      "sts:AssumeRoleWithWebIdentity",
    ]

    effect = "Allow"

    condition {
      test     = "StringLike"
      variable = "token.actions.githubusercontent.com:sub"

      values = [
        for repository in local.repositories : "repo:%{if length(regexall(":+", repository)) > 0}${repository}%{else}${repository}:*%{endif}"
      ]
    }

    principals {
      type = "Federated"

      identifiers = [
        aws_iam_openid_connect_provider.this.arn,
      ]
    }
  }
}

resource "aws_iam_role" "this" {
  name                 = "github-oidc-provider-aws"
  description          = "Role assumed by the GitHub OIDC provider"
  assume_role_policy   = data.aws_iam_policy_document.this.json
  max_session_duration = 3600

  depends_on = [
    aws_iam_openid_connect_provider.this,
  ]
}

resource "aws_iam_role_policy_attachment" "this" {
  count      = length(local.assume_role_attach_policies)
  policy_arn = local.assume_role_attach_policies[count.index]
  role       = aws_iam_role.this.name
}
```

#### Authenticate with AWS in the GitHub workflow

Define workflow steps within a GitHub workflow that leverages the well-maintained `aws-actions/configure-aws-credentials` action to retrieve temporary credentials using OIDC:

```yaml
permissions:
  id-token: write
  contents: read
jobs:
  demo:
    runs-on: ubuntu-latest
    steps:
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: "arn:aws:iam::<Your AWS account ID>:role/github-oidc-provider-aws"
          role-session-name: github-workflow-session
          aws-region: eu-west-2
```

#### Utilise temporary credentials

Subsequent workflow steps can utilise the retrieved temporary credentials to interact with AWS services securely.

## Conclusion

OIDC provides a powerful approach to securing CI/CD pipelines in highly regulated industries like banking and insurance. By integrating OIDC with GitHub Actions and Terraform, these institutions can achieve dynamic, secure authentication while maintaining the agility of their development processes. Remember, this is a foundational step, and a layered security approach is crucial for optimal protection of sensitive personal data.