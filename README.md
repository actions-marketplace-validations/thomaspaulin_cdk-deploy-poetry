# cdk-deploy-poetry

Deploy Python CDK applications that use Poetry for their dependency management.

## Usage
```
on: [push]

# See https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services#adding-permissions-settings
permissions:
    id-token: write
    contents: read

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # You must configure the AWS crentials yourself
      - name: configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v3
        with:
          audience: sts.amazonaws.com
          role-to-assume: ${{ secrets.ASSUMED_ROLE_ARN }}
          aws-region: ${{ vars.AWS_REGION }}
        
      - name: "AWS CDK Deploy"
        uses: thomaspaulin/cdk-deploy-poetry@master
        with:
          python-version: "3.11"
          role-to-assume: ${{ secrets.ASSUMED_ROLE_ARN }}
          aws-account-id: $${{ secrets.AWS_ACCOUNT_ID }}
          aws-region: ${{ vars.AWS_REGION }}
```
## AWS Trust Policies

Don't forget to edit the role your pipeline assumes to trust your repo and branch:
```
{
    "Effect": "Allow",
    "Principal": {
        "Federated": "<your-identity-provider-arn-goes-here>/token.actions.githubusercontent.com"
    },
    "Action": "sts:AssumeRoleWithWebIdentity",
    "Condition": {
        "StringLike": {
            "token.actions.githubusercontent.com:sub": "repo:<github-org-or-username>/<repo-name>:ref:refs/heads/<branch-name>",
            "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
        }
    }
}
```
