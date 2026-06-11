# terraform-security-linter

Browser-based linter for Terraform/HCL. Paste a `.tf` file, get findings on common AWS misconfigurations and embedded secrets.

**Live demo:** https://0xelitesystem.github.io/terraform-security-linter/

Single HTML file. No build step, no dependencies, no network calls.

## What it checks

Resource-level rules across eight common AWS resource types:

- **`aws_s3_bucket`**, public ACLs, missing versioning, missing server-side encryption, missing block-public-access
- **`aws_security_group`**, ingress from `0.0.0.0/0` on sensitive ports (22, 3389, 3306, 5432, 6379, 27017), egress to `0.0.0.0/0` on all ports
- **`aws_db_instance` / `aws_rds_cluster`**, `publicly_accessible = true`, missing `storage_encrypted`, missing `backup_retention_period`, default master usernames
- **`aws_iam_policy` / `aws_iam_role_policy`**, wildcard actions on wildcard resources (cross-references the iam-policy-analyzer rule set)
- **`aws_instance`**, IMDSv1 not disabled, public IP on private subnets, missing root-volume encryption
- **`aws_lambda_function`**, environment variables that look like secrets, missing tracing, no reserved concurrency
- **`aws_kms_key`**, `enable_key_rotation = false`, overly permissive key policies
- **`aws_cloudtrail`**, single-region trails, no log file validation, no KMS encryption

Plus a secret heuristic that flags string literals matching AWS access keys, generic API tokens, private key headers, and high-entropy strings in `locals` and `variable default` blocks.

## Severity scale

| Tag | Meaning |
|-----|---------|
| critical | Public exposure of data or credentials, hardcoded secrets |
| high | Encryption disabled, broad network exposure, missing audit logging |
| medium | Missing defense-in-depth control |
| low | Hygiene (missing tags, weak naming) |
| info | Pattern flagged for review without strong recommendation |

## What this is NOT

Not a replacement for `tfsec`, `checkov`, or `terrascan`. It does a single-file static parse, so it cannot follow `module` references, expand variables across files, or evaluate `for_each` expansions. Use it for quick pull-request review or a second pair of eyes; pin the heavy tools in CI.

## Privacy

The HCL you paste stays in the browser. No analytics, no storage, no network requests after page load.

## Samples

Three samples are wired to header buttons: a public S3 bucket, an open security group with hardcoded credentials, and a misconfigured RDS instance.

## Related repositories

Part of a 10-repo security audit set.

Browser-based audit tools:
- [iam-policy-analyzer](https://github.com/0xelitesystem/iam-policy-analyzer)
- [kubernetes-manifest-security-scanner](https://github.com/0xelitesystem/kubernetes-manifest-security-scanner)
- [session-cookie-auditor](https://github.com/0xelitesystem/session-cookie-auditor)
- [regex-redos-checker](https://github.com/0xelitesystem/regex-redos-checker)

Reference collections:
- [incident-response-runbooks](https://github.com/0xelitesystem/incident-response-runbooks)
- [ai-llm-security-audit](https://github.com/0xelitesystem/ai-llm-security-audit)
- [api-security-audit-checklist](https://github.com/0xelitesystem/api-security-audit-checklist)
- [secrets-leak-response-runbook](https://github.com/0xelitesystem/secrets-leak-response-runbook)
- [threat-modeling-worksheets](https://github.com/0xelitesystem/threat-modeling-worksheets)

## License

MIT. See [LICENSE](LICENSE).
