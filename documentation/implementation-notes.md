# Implementation Notes

## Project Objective

Implement tag-based access control in AWS so a restricted IAM user can manage development EC2 resources without being able to modify production resources.

## AWS Services

- AWS Identity and Access Management
- Amazon EC2
- AWS Management Console

## Implementation Process

### 1. Create EC2 Resources

Two EC2 instances were used to represent separate environments:

- Development
- Production

### 2. Apply Environment Tags

The development instance was assigned:

```text
Env=development
```

The production instance was assigned:

```text
Env=production
```

### 3. Create the Custom IAM Policy

The policy contained separate statements for:

- Development-resource access
- EC2 resource visibility
- Denial of EC2 tag changes

### 4. Create the IAM User Group

A user group was created for development-level access.

The custom IAM policy was attached to the group rather than directly to an individual user.

### 5. Create the Restricted IAM User

An IAM user representing an intern was created and added to the development user group.

### 6. Test Production Access

The restricted user attempted to stop the production EC2 instance.

Expected result:

```text
Access denied
```

Actual result:

```text
Access denied
```

### 7. Test Development Access

The same user attempted to stop the development EC2 instance.

Expected result:

```text
Action allowed
```

Actual result:

```text
Action allowed
```

## Security Result

The test demonstrated that the policy permitted management of development resources without granting equivalent access to production resources.

## Key Lessons

- IAM permissions can be assigned through user groups.
- Resource tags can provide access-control context.
- Explicit deny takes priority over an allow.
- Read-only EC2 visibility may require separate `Describe` permissions.
- Tag modification must be restricted when tags control authorization.
- Permissions should be tested using the restricted identity rather than an administrator account.
