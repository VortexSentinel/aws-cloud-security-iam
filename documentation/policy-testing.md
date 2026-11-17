# IAM Policy Testing

## Testing Objective

Verify that the restricted IAM user could manage development EC2 resources without being able to manage production EC2 resources.

## Test Identity

Testing was performed using a restricted IAM user assigned to the development user group.

No administrator credentials were used for the permission tests.

## Test 1: EC2 Resource Visibility

### Action

Open the Amazon EC2 console and view the available instances.

### Expected Result

The IAM user should be able to view EC2 resources because the policy permits:

```text
ec2:Describe*
```

### Result

Passed.

---

## Test 2: Stop the Production Instance

### Resource Tag

```text
Env=production
```

### Expected Result

The action should be denied because the policy only allows management actions on resources tagged:

```text
Env=development
```

### Result

Passed. The production action was denied.

---

## Test 3: Stop the Development Instance

### Resource Tag

```text
Env=development
```

### Expected Result

The action should be allowed because the resource matches the IAM policy condition.

### Result

Passed. The development instance was stopped successfully.

---

## Test 4: Modify Resource Tags

### Actions Restricted

```text
ec2:CreateTags
ec2:DeleteTags
```

### Expected Result

The user should not be able to create or delete EC2 tags.

### Security Purpose

This restriction prevents the user from changing a production resource’s environment tag and bypassing the access-control condition.

---

## Test Summary

| Test | Expected | Result |
|---|---|---|
| View EC2 resources | Allowed | Passed |
| Stop production instance | Denied | Passed |
| Stop development instance | Allowed | Passed |
| Change EC2 tags | Denied | Restricted by policy |

## Final Validation

The policy successfully separated development and production permissions using EC2 resource tags.
