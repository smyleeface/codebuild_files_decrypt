CodeBuild Files Decrypt
=======================

Lambda function for S3 event used to decrypt and move the files encrypted and published by CodeBuild.

CodeBuild Setup
===============

In the section `Artifacts: Where to put the artifacts from this build project`
- _Type_: `Amazon S3`
- _Name_: `CodeBuildArtifacts`
- Leave the _Path_ empty
- _Namespace type_: `None`
- Select the _Bucket name_ where you want the artifacts placed 

Lambda Trigger Setup
====================

Decrypts files in `CodeBuildArtifacts` and places all files one directory up in the same S3 bucket.

- Add S3 Trigger

Then Configure triggers
- Select the _Bucket_ where the artifacts will be placed
- Choose `Object Created (All)` for _Event type_
- _Prefix_: CodeBuildArtifacts

Lambda Policy
=============

In the policy below, replace the following:

- `BUCKET_NAME` for your bucket name
- `KMS_ARN` with the ARN of your managed encryption key `aws/s3`

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream",
                "logs:CreateLogGroup",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:PutObject",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::BUCKET_NAME/*"
            ]
        },
        {
            "Sid": "VisualEditor2",
            "Effect": "Allow",
            "Action": "kms:*",
            "Resource": "KMS_ARN"
        }
    ]
}
```