---
title: CREATE STAGE
---

Creates a stage.

## Syntax

```sql
-- Internal stage
CREATE STAGE [ IF NOT EXISTS ] <internal_stage_name>
  [ FILE_FORMAT = ( { TYPE = { PARQUET | CSV | TSV | NDJSON } [ formatTypeOptions ] ) } ]
  [ COPY_OPTIONS = ( copyOptions ) ]
  [ COMMENT = '<string_literal>' ]

-- External stage
CREATE STAGE [ IF NOT EXISTS ] <external_stage_name>
    externalStageParams
  [ FILE_FORMAT = ( { TYPE = { PARQUET | CSV | TSV | NDJSON } [ formatTypeOptions ] ) } ]
  [ COPY_OPTIONS = ( copyOptions ) ]
  [ COMMENT = '<string_literal>' ]
```

Where:

### externalStageParams

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

<Tabs groupId="externalstageparams">

<TabItem value="Amazon S3-compatible Storage" label="Amazon S3-compatible Storage">

```sql
externalStageParams ::=
  URL = 's3://<bucket>[<path/>]'
  CONNECTION = (
        ENDPOINT_URL = 'https://<endpoint-URL>'
        ACCESS_KEY_ID = '<your-access-key-ID>'
        SECRET_ACCESS_KEY = '<your-secret-access-key>'
        REGION = '<region-name>'
        ENABLE_VIRTUAL_HOST_STYLE = 'true'|'false'
  )
```

| Parameter                 | Description                                                                                                                                                                           | Required     |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| URL                       | External files located at the AWS S3 compatible object storage.                                                                                                                       | **Required** |
| ENDPOINT_URL              | The bucket endpoint URL starting with "https://". To use a URL starting with "http://", set `allow_insecure` to `true` in the [storage] block of the file `databend-query-node.toml`. | Optional     |
| ACCESS_KEY_ID             | Your access key ID for connecting the AWS S3 compatible object storage. If not provided, Databend will access the bucket anonymously.                                                 | Optional     |
| SECRET_ACCESS_KEY         | Your secret access key for connecting the AWS S3 compatible object storage.                                                                                                           | Optional     |
| REGION                    | AWS region name. For example, us-east-1.                                                                                                                                              | Optional     |
| ENABLE_VIRTUAL_HOST_STYLE | If you use virtual hosting to address the bucket, set it to "true".                                                                                                                   | Optional     |

</TabItem>

<TabItem value="Azure Blob Storage" label="Azure Blob Storage">

```sql
externalStageParams ::=
  URL = 'azblob://<container>[<path/>]'
  CONNECTION = (
        ENDPOINT_URL = 'https://<endpoint-URL>'
        ACCOUNT_NAME = '<your-account-name>'
        ACCOUNT_KEY = '<your-account-key>'
  )
```

| Parameter    | Description                                                                                                                                                                              | Required     |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| URL          | External files located at the Azure Blob storage.                                                                                                                                        | **Required** |
| ENDPOINT_URL | The container endpoint URL starting with "https://". To use a URL starting with "http://", set `allow_insecure` to `true` in the [storage] block of the file `databend-query-node.toml`. | Optional     |
| ACCOUNT_NAME | Your account name for connecting the Azure Blob storage. If not provided, Databend will access the container anonymously.                                                                | Optional     |
| ACCOUNT_KEY  | Your account key for connecting the Azure Blob storage.                                                                                                                                  | Optional     |

</TabItem>

<TabItem value="Google Cloud Storage" label="Google Cloud Storage">

```sql
externalLocation ::=
  URL = 'gcs://<bucket>[<path>]'
  CONNECTION = (
        ENDPOINT_URL = 'https://<endpoint-URL>'
        CREDENTIAL = '<your-credential>'
  )
```

| Parameter    | Description                                                                                                                                                                              | Required     |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| URL          | External files located at the Google Cloud Storage                                                                                                                                       | **Required** |
| ENDPOINT_URL | The container endpoint URL starting with "https://". To use a URL starting with "http://", set `allow_insecure` to `true` in the [storage] block of the file `databend-query-node.toml`. | Optional     |
| CREDENTIAL   | Your credential for connecting the GCS. If not provided, Databend will access the container anonymously.                                                                                 | Optional     |

</TabItem>

<TabItem value="Huawei Object Storage" label="Huawei Object Storage">

```sql
externalLocation ::=
  URL = 'obs://<bucket>[<path>]'
  CONNECTION = (
        ENDPOINT_URL = 'https://<endpoint-URL>'
        ACCESS_KEY_ID = '<your-access-key-id>'
        SECRET_ACCESS_KEY = '<your-secret-access-key>'
  )
```

| Parameter         | Description                                                                                                                                                                              | Required     |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| URL               | External files located at the obs                                                                                                                                                        | **Required** |
| ENDPOINT_URL      | The container endpoint URL starting with "https://". To use a URL starting with "http://", set `allow_insecure` to `true` in the [storage] block of the file `databend-query-node.toml`. | **Required** |
| ACCESS_KEY_ID     | Your access key ID for connecting the OBS. If not provided, Databend will access the bucket anonymously.                                                                                 | Optional     |
| SECRET_ACCESS_KEY | Your secret access key for connecting the OBS.                                                                                                                                           | Optional     |

</TabItem>

<TabItem value="WebHDFS Storage" label="WebHDFS Storage">

```sql
externalLocation ::=
  URL = "webhdfs://<endpoint_url[<path>]"
  CONNECTION = (
    [ HTTPS = 'true'|'false' ]
    [ DELEGATION = '<your-delegation-token>' ]
  )
```

| Parameter  | Description                                                                                                                                                                                                                                         | Required     |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| URL        | External endpoint and location of files.                                                                                                                                                                                                            | **Required** |
| HTTPS      | Connect to RESTful API with HTTP or HTTPS, set to `true` will use `HTTPS`, else `HTTP`. Use `HTTPS` by default. To use a URL starting with "http://", set `allow_insecure` to `true` in the [storage] block of the file `databend-query-node.toml`. | Optional     |
| DELEGATION | Delegation token of WebHDFS for authentication. If not set, no tokens will be used in further operations.                                                                                                                                           | Optional     |

</TabItem>

</Tabs>

### formatTypeOptions

For details about `FILE_FORMAT`, see [Input & Output File Formats](../../../13-sql-reference/50-file-format-options.md).

### copyOptions

```
copyOptions ::=
  [ SIZE_LIMIT = <num> ]
  [ PURGE = <bool> ]
```

| Parameters           | Description                                                                                                                   | Required |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------- | -------- |
| `SIZE_LIMIT = <num>` | Number (> 0) that specifies the maximum rows of data to be loaded for a given COPY statement. Default `0`                     | Optional |
| `PURGE = <bool>`     | True specifies that the command will purge the files in the stage if they are loaded successfully into table. Default `false` | Optional |

## Examples

### Internal Stages

```sql
CREATE STAGE my_internal_stage;
```

### External Stages

```sql
CREATE STAGE my_s3_stage URL='s3://load/files/' CONNECTION = (ACCESS_KEY_ID = '<your-access-key-id>' SECRET_ACCESS_KEY = '<your-secret-access-key>');
```

```sql
DESC STAGE my_s3_stage;
+-------------+------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------+---------+
| name        | stage_type | stage_params                                                                                                                                                           | copy_options                                  | file_format_options                                                                                                | comment |
+-------------+------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------+---------+
| my_s3_stage | External   | StageParams { storage: S3(StageS3Storage { bucket: "load", path: "/files/", credentials_aws_key_id: "", credentials_aws_secret_key: "", encryption_master_key: "" }) } | CopyOptions { on_error: None, size_limit: 0 } | FileFormatOptions { format: Csv, skip_header: 0, field_delimiter: ",", record_delimiter: "\n", compression: None } |         |
+-------------+------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------+---------+
```
