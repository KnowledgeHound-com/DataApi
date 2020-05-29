# S3 Permissions
KnowledgeHound needs read access to your files in S3 to process them. We recommend Object ACLs or Bucket Policies to grant access by KnowledgeHound.

## Object ACL
Object ACLs can be set through either the [AWS Console](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-object-permissions.html) or the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/s3api/put-object-acl.html). The KnowledgeHound Canonical ID is: `7b8f485aec57cb9e3956763470093ea2e329b5531544d78ad6618ecdf1deffba`.

Example CLI command:
`aws s3api put-object-acl --bucket mybucket --key path/to/object --grant-read id=7b8f485aec57cb9e3956763470093ea2e329b5531544d78ad6618ecdf1deffba`

## Bucket Policy
Alternatively, you can also set a bucket policy in the [AWS Console](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html) or the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/s3api/put-bucket-policy.html). The KnowledgeHound Account ID is: `548906291368`.

Example policy:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::548906291368:root"
            },
            "Action": "s3:GetObject",
            "Resource": [
                "arn:aws:s3:::mybucket/path/to/object",
                "arn:aws:s3:::mybucket/path/to/directory/*"
            ]
        }
    ]
}
```

Example CLI command:
`aws s3api put-bucket-policy --bucket mybucket --policy file://bucket_policy.json`
