# ❄️ Snowflake Email Search and Data Masking Using Snowpark
This Snowflake Snowpark Python script is designed to **search for, log, and mask sensitive email-related information** across multiple databases in a Snowflake environment. It identifies records related to a specific email address and updates personally identifiable information (PII) to anonymize the data.

---

## Features
- Searches for a given email address across all tables (excluding views) in specified databases and schemas.
- Dynamically builds and logs full SQL queries to locate matching records.
- Identifies "customer-related" and "PII-related" columns using intelligent pattern matching.
- Logs results and anonymizes matching data by:
  - Replacing customer name fields with `"Removed"`
  - Clearing PII fields (e.g., email, address, phone)
- Appends log entries to a central audit table.
- Tracks summary statistics of the anonymization for downstream reporting.

---

## Configuration
Update the following variables in the script:
- email_submitted = "tsteussie@gmail.com"  # Target email to search
- databases = ["DEV_COPY", "PRD_COPY"]  # Databases to search
- schema = "PUBLIC"  # Schema to use

## Output Tables
The script logs to the following Snowflake tables:
- PRD_MART.DATA_ANONYMIZATION.SEARCH_RECORDS_LOG: Detailed logs of all search matches and affected columns.
- PRD_MART.DATA_ANONYMIZATION.COMPLETED_REQUESTS: Summary log with counts of affected databases, tables, and columns.

## PII Detection
The following patterns are used to detect columns for masking:
- Customer-related columns: NAME_%, CUST_%, SHPR_%, BILL_%, ATTN_%, TITL_%, AUTH_%, etc.
- Related columns (addresses, phones, etc.): EMAIL, ADDR%, STREET, PO, CITY, ZIP, PHONE, FAX, etc.

## Logging
Each execution logs the full SQL query executed
Full object name (database.schema.table)
Columns masked, row counts

## Example Outputs
- Executing search query: SELECT EMAIL FROM PUBLIC.CONTACTS WHERE EMAIL IN ('example@email.com');
- Full object name: PRD_COPY.PUBLIC.CONTACTS
- Executing update SQL: UPDATE PRD_COPY.PUBLIC.CONTACTS SET FIRST_NAME = 'Removed', LAST_NAME = 'Removed', EMAIL = '' WHERE EMAIL IN ('example@email.com');
- Updated PRD_COPY.PUBLIC.CONTACTS — Rows affected: 2

## Prerequisites
- Python 3.11+
- Snowpark for Python
- Snowflake user credentials with SYSADMIN role (or appropriate access)
- Tables must be writable (UPDATE privileges will be granted dynamically)

## Deployment
This script is intended to be run inside a Snowflake Worksheet or deployed in a Snowpark for Python UDF or Stored Procedure if needed.

## License
This project is internal and confidential. For usage or distribution rights, please contact the repository maintainer.
