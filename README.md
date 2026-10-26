from pathlib import Path
import shutil, zipfile, pypandoc

base = Path("/mnt/data")
repo = base / "aws-rds-dynamodb-cornell-notes"
assets = repo / "assets"
assets.mkdir(parents=True, exist_ok=True)

source_image = base / "AWS RDS and DynamoDB Cornell Notes Infographic(1).png"
image_dest = assets / "aws-rds-dynamodb-cornell-notes.png"
shutil.copy2(source_image, image_dest)

readme_text = r"""<div align="center">

# AWS RDS & DynamoDB — Cornell Notes

**A concise visual study guide to Amazon RDS and Amazon DynamoDB**

[![AWS](https://img.shields.io/badge/Cloud-AWS-orange?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Databases](https://img.shields.io/badge/Topics-RDS%20%7C%20DynamoDB-2563eb)](https://aws.amazon.com/products/databases/)
[![Notes](https://img.shields.io/badge/Format-Cornell%20Notes-0f766e)](#)

</div>

---

## Overview

This repository contains a Cornell Notes infographic for reviewing **Amazon Relational Database Service (Amazon RDS)** and **Amazon DynamoDB**. It is designed for cloud-computing learners who want a quick, practical reference while studying AWS database services and building hands-on labs.

## Study Notes

![AWS RDS and DynamoDB Cornell Notes infographic](assets/aws-rds-dynamodb-cornell-notes.png)

## Topics Covered

- **RDS vs. DynamoDB** — relational and NoSQL database models, schema, keys, and common use cases.
- **Creating and managing resources** — core setup considerations for RDS instances and DynamoDB tables.
- **Insertion and deletion** — example SQL statements and DynamoDB API/CLI operation patterns.
- **Connecting AWS services** — common connectivity patterns for EC2 → RDS and Lambda → DynamoDB.
- **IAM roles and policies** — least-privilege access and execution roles.
- **AWS CLI** — example commands for inspecting and working with database resources.
- **Troubleshooting** — diagnosing DynamoDB key-schema validation errors.
- **Choosing a database** — matching the database service to application access patterns and workload needs.

## Quick Comparison

| Feature | Amazon RDS | Amazon DynamoDB |
|---|---|---|
| Database model | Relational SQL | NoSQL key-value and document |
| Data structure | Tables, rows, and columns | Tables, items, and attributes |
| Access pattern | SQL queries, joins, and relational relationships | Key-based access using partition keys and optional sort keys |
| Schema | Defined relational schema | Flexible item attributes; key schema still matters |
| Common fit | Relational data, transactions, and complex queries | High-scale, low-latency access with predictable key-based patterns |
| Capacity approach | Instance and storage configuration, depending on engine and deployment | On-demand or provisioned capacity modes |

> **Important:** Service features, limits, pricing, and console workflows can change. Check the official AWS documentation before using these notes for production decisions.

## Example AWS CLI Commands

List RDS database instances:

```bash
aws rds describe-db-instances
```

List DynamoDB tables:

```bash
aws dynamodb list-tables
```

Describe a DynamoDB table:

```bash
aws dynamodb describe-table --table-name Users
```

Get an item using a key that matches the table's actual primary-key schema:

```bash
aws dynamodb get-item \
  --table-name Users \
  --key '{"userId":{"S":"u101"}}'
```

**Note:** Replace `Users`, `userId`, and `u101` with the table name, key attribute, and value from your own table. The key's attribute name and data type must match the table schema exactly.

## Key Learning Takeaways

- Choose **RDS** when your application benefits from relational structure, SQL, joins, and transactional consistency.
- Choose **DynamoDB** when your access patterns suit key-based lookups and you need a managed NoSQL service that can scale.
- Use IAM roles and least-privilege policies instead of hardcoding credentials.
- Keep database resources private where possible and allow only the required network paths.
- For DynamoDB key errors, verify the exact partition-key and sort-key names and types.

## Repository Structure

```text
aws-rds-dynamodb-cornell-notes/
├── README.md
└── assets/
    └── aws-rds-dynamodb-cornell-notes.png
```

## Official AWS Documentation

- [Amazon RDS User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)
- [Amazon DynamoDB Developer Guide](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)
- [AWS Identity and Access Management (IAM)](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)
- [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/)

## How to Use

1. Open the infographic above for a visual revision.
2. Review the comparison table and key takeaways.
3. Practise the CLI commands in your own AWS environment.
4. Confirm your IAM permissions, region, table schema, and network configuration before troubleshooting.

---

<div align="center">

**Created for learning and revision**

*Keep Learning. Keep Building. ☁️*

</div>
"""

readme_path = repo / "README.md"
pypandoc.convert_text(
    readme_text,
    to="md",
    format="md",
    outputfile=str(readme_path),
    extra_args=["--standalone"]
)

zip_path = base / "aws-rds-dynamodb-cornell-notes-github.zip"
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as zf:
    for path in repo.rglob("*"):
        if path.is_file():
            zf.write(path, path.relative_to(repo.parent))

print(f"Created README: {readme_path}")
print(f"Created GitHub-ready package: {zip_path}")
