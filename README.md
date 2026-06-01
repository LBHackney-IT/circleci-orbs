# CircleCI Orbs — LBHackney-IT

CircleCI namespace: [lbh-hackit](https://circleci.com/developer/orbs?query=lbh-hackit)

## Orbs

### aws-assume-role

[lbh-hackit/aws_assume_role](https://circleci.com/developer/orbs/orb/lbh-hackit/aws_assume_role)

Assumes an AWS IAM role from within a CircleCI pipeline and exports temporary credentials for use in subsequent
steps. This avoids storing long-lived AWS keys in environment variables, keeping deployments compliant with
current security best practices.

#### Usage

```yaml
orbs:
  aws_assume_role: lbh-hackit/aws_assume_role@0.1.1

commands:
  assume-role-and-persist-workspace:
    parameters:
      aws-account:
        type: string
    steps:
      - checkout
      - aws_assume_role/assume_role:
          account: <<parameters.aws-account>>
          profile_name: default
          role: LBH_Circle_CI_Deployment_Role
      - persist_to_workspace:
          root: /home/circleci
          paths:
            - .aws
```

#### Parameters

| Parameter | Type | Default | Description |
|---|---|---|---|
| `account` | string | — | AWS account ID to assume the role in |
| `role` | string | — | IAM role name to assume |
| `profile_name` | string | `default` | AWS profile name to write credentials to |
| `access_key_id` | env_var_name | `AWS_ACCESS_KEY_ID` | Env var holding the IAM user access key |
| `secret_access_key` | env_var_name | `AWS_SECRET_ACCESS_KEY` | Env var holding the IAM user secret key |

#### Prerequisites

- `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` must be available in the job via a CircleCI context
- A `.circleci/config` file must exist in the repository — this is copied to `~/.aws/config` to set the AWS region
- `jq` must be available on the executor image

## Releasing

Releases are automated via the CircleCI pipeline:

- **Merge to `development`** — publishes a dev version (`@dev:<sha>`) for testing in consuming pipelines
- **Merge to `main`** — promotes to a new production patch release

The pipeline requires an `circleci-orb-publishing` context in the CircleCI org settings containing a `CIRCLECI_CLI_TOKEN`
with namespace admin access for `lbh-hackit`.
