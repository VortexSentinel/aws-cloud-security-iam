# EC2 Resource-Tagging Configuration

## Purpose

Amazon EC2 resource tags were used to distinguish development resources from production resources.

The IAM policy evaluates the `Env` tag before allowing the restricted IAM user to perform EC2 management actions.

## Tagging Standard

| Environment | Tag key | Tag value |
|---|---|---|
| Development | `Env` | `development` |
| Production | `Env` | `production` |

## Development Instance

```text
Key: Env
Value: development
```

The custom IAM policy permits EC2 management actions on resources with this tag value.

## Production Instance

```text
Key: Env
Value: production
```

The custom IAM policy does not grant the restricted IAM user permission to manage resources with this tag value.

## Why Tag Modification Is Denied

The IAM policy explicitly denies:

```text
ec2:CreateTags
ec2:DeleteTags
```

Without this restriction, a user could potentially change a production resource from:

```text
Env=production
```

to:

```text
Env=development
```

and attempt to bypass the access-control policy.

## Tag Governance Considerations

For a production environment, this design could be strengthened using:

- AWS Organizations tag policies
- Service Control Policies
- AWS Config rules
- Permission boundaries
- Standardized resource-naming conventions
