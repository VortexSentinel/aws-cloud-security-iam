# AWS Cloud Security with IAM

## Project Overview

Implemented a tag-based access-control system in AWS using IAM users, user groups, custom JSON policies, and Amazon EC2 resource tags.

The project separates development and production EC2 resources using the `Env` tag. An IAM user representing an intern was allowed to manage development resources while being prevented from modifying production resources.

The final permission test confirmed that the intern could stop the development instance but could not stop the production instance.

---

## Project Objectives

- Create separate development and production EC2 resources
- Organize AWS resources using tags
- Create a custom IAM policy using JSON
- Apply permissions through an IAM user group
- Create an IAM user with restricted access
- Test access from the IAM user’s perspective
- Protect production resources from unauthorized actions
- Apply the principle of least privilege

---

## Architecture

```text
AWS Account
│
├── Amazon EC2
│   ├── Development Instance
│   │   └── Tag: Env = development
│   │
│   └── Production Instance
│       └── Tag: Env = production
│
└── AWS IAM
    ├── IAM User
    │   └── Intern
    │
    ├── IAM User Group
    │   └── Development Group
    │
    └── Custom IAM Policy
        ├── Allow EC2 actions on development resources
        ├── Allow EC2 describe actions
        └── Deny EC2 tag creation and deletion
```

---

## AWS Services Used

| AWS service | Purpose |
|---|---|
| AWS Identity and Access Management | Identity, permission, group, and policy management |
| Amazon EC2 | Development and production compute resources |
| AWS Management Console | Resource configuration and permission testing |

---

## Security Concepts Demonstrated

- Identity and Access Management
- Principle of least privilege
- Tag-based access control
- Environment separation
- Group-based permission management
- Explicit deny statements
- IAM policy evaluation
- Production-resource protection
- Permission testing

---

## Resource-Tagging Strategy

The EC2 instances were separated using the `Env` resource tag.

| Resource | Tag key | Tag value |
|---|---|---|
| Development EC2 instance | `Env` | `development` |
| Production EC2 instance | `Env` | `production` |

The IAM policy evaluates the `Env` tag before allowing the IAM user to perform EC2 management actions.

---

## IAM Policy Design

The custom policy was designed to provide the intern with the access required for development work without granting control over production resources.

### Policy behavior

- Allow EC2 management actions on resources tagged `Env=development`
- Allow EC2 describe actions so resources can be viewed in the console
- Explicitly deny EC2 tag creation and deletion
- Prevent management actions on resources tagged `Env=production`

Preventing tag changes is important because an IAM user should not be able to change a production resource’s tag to `development` and bypass the access-control policy.

The sanitized JSON policy is available in:

```text
configurations/development-access-policy.json
```

---

## Implementation Walkthrough

### 1. EC2 Resource Tagging

Two EC2 instances were assigned different environment tags:

- `Env=development`
- `Env=production`

The tags provide the resource context used by the IAM policy.

### 2. Custom IAM Policy

A multi-statement JSON policy was created to control EC2 access according to resource tags.

The policy combines:

- `Effect`
- `Action`
- `Resource`
- `Condition`

### 3. AWS Account Alias

An AWS account alias was configured to provide a more user-friendly console sign-in address.

Sensitive account and sign-in information has been removed from the public screenshots.

### 4. IAM User Group

A development user group was created, and the custom IAM policy was attached to the group.

Users added to this group inherit the group’s permissions.

### 5. IAM User

An IAM user representing an intern was created and added to the development user group.

Testing was performed while signed in as this restricted IAM user.

### 6. Permission Testing

The policy was validated by attempting to stop both EC2 instances.

| Test | Expected result | Actual result |
|---|---|---|
| Stop production instance | Denied | Denied |
| Stop development instance | Allowed | Allowed |

The successful tests confirmed that the tag-based access controls behaved as intended.

---

## Project Images

### 1. EC2 Resource Tags

images/01-ec2-resource-tags.png

Development and production EC2 resources are separated using the `Env` tag.

---

### 2. IAM JSON Policy

images/02-iam-json-policy.png

The custom JSON policy controls EC2 actions according to the resource’s environment tag.

---

### 3. AWS Account Alias

images/03-aws-account-alias-redacted.png

An AWS account alias provides a more convenient console sign-in address. Sensitive information has been redacted.

---

### 4. IAM User Sign-In

images/04-iam-user-sign-in-redacted.png

The IAM user signed in using restricted credentials. Identifying and credential-related information has been redacted.

---

### 5. Production Access Denied

images/05-production-access-denied-redacted.png

The attempt to stop the production instance was denied because the resource was outside the intern’s permitted environment.

---

### 6. Development Action Allowed

images/06-development-instance-stopped-redacted.png

The development instance was stopped successfully, confirming that the policy allowed the required action on development resources.

---

## Testing Results

### Production Environment Test

The IAM user attempted to stop the production EC2 instance.

**Result:** Access denied.

This confirmed that the user could not manage resources tagged `Env=production`.

### Development Environment Test

The IAM user attempted to stop the development EC2 instance.

**Result:** Action successful.

This confirmed that the user could manage resources tagged `Env=development`.

---

## Security Measures

- Development and production environments separated using tags
- Permissions assigned through an IAM user group
- Tag-based policy conditions applied
- Production-management actions restricted
- Tag modification explicitly denied
- Permissions tested using the restricted IAM user
- Sensitive identifiers removed from public screenshots
- No AWS credentials or private keys stored in the repository

---

## Challenges Faced

### Understanding Multi-Statement IAM Policies

The most challenging part was understanding how multiple IAM policy statements work together.

The policy required separate statements for:

- Development-resource access
- Read-only EC2 visibility
- Explicit denial of tag modifications

Testing the policy from the IAM user’s account helped demonstrate how AWS evaluates allow and deny statements.

### Validating Tag-Based Permissions

The policy depended on the EC2 resources having the correct `Env` tag.

Testing both instances confirmed that the condition applied to development resources without granting the same access to production resources.

---

## Key Learnings

This project provided practical experience with:

- IAM users and user groups
- Custom JSON policies
- IAM policy statements and conditions
- Tag-based access control
- EC2 resource tagging
- Least-privilege permissions
- Explicit deny behavior
- Development and production separation
- Access-control testing

---

## Business Value

This access-control model enables organizations to give developers and interns access to development resources without exposing production workloads to unnecessary risk.

The approach can help:

- Reduce accidental production changes
- Standardize access through IAM groups
- Improve environment separation
- Apply permissions consistently using resource tags
- Support scalable identity and permission management

---

## Future Improvements

- Replace long-term IAM users with IAM Identity Center
- Require multi-factor authentication
- Add CloudTrail logging for IAM and EC2 activity
- Add CloudWatch alerts for denied production actions
- Implement the policy through Terraform
- Use permission boundaries for additional protection
- Add automated IAM policy validation
- Apply more specific EC2 actions instead of broad permissions

---

## Repository Structure

```text
aws-cloud-security-iam/
├── README.md
├── LICENSE
├── images/
│   ├── README.md
│   ├── 01-ec2-resource-tags.png
│   ├── 02-iam-json-policy.png
│   ├── 03-aws-account-alias-redacted.png
│   ├── 04-iam-user-sign-in-redacted.png
│   ├── 05-production-access-denied-redacted.png
│   └── 06-development-instance-stopped-redacted.png
├── configurations/
│   ├── README.md
│   ├── development-access-policy.json
│   └── resource-tagging.md
└── documentation/
    ├── README.md
    ├── implementation-notes.md
    └── policy-testing.md
```

---

## Original Project Reference

This project was originally completed as part of the NextWork Cloud Engineering learning path.

To showcase practical cloud-security skills, the repository was expanded with:

- Professional documentation
- IAM policy explanations
- Security considerations
- Access-control test results
- Sanitized configuration files
- Redacted project screenshots
- Business-value analysis
- Future security improvements

These additions go beyond the original project deliverables and better demonstrate practical AWS IAM skills.

---

## Project Status

✅ Completed

---

## Author

### Rajnish Jha

Aspiring Cloud and DevOps Engineer focused on AWS, Linux, Docker, Git, security, and automation.

### Connect With Me

- LinkedIn: <https://www.linkedin.com/in/rajnish-k-jha>
- GitHub: <https://github.com/VortexSentinel>
- Email: <rajnish96jha@gmail.com>
