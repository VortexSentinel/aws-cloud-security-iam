# Configurations

This directory contains sanitized IAM policy and EC2 resource-tagging configuration used in the AWS Cloud Security with IAM project.

## Files

### `development-access-policy.json`

Custom IAM policy that:

- Allows EC2 actions on development-tagged resources
- Allows EC2 `Describe` actions
- Denies EC2 tag creation and deletion

### `resource-tagging.md`

Documents the tagging strategy used to separate development and production EC2 resources.

## Security Notice

This directory does not contain:

- AWS access keys
- Secret access keys
- Console passwords
- AWS account IDs
- Private keys
- Session tokens
- Unredacted ARNs

All published configurations have been sanitized for public use.
