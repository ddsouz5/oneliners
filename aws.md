    aws batch describe-jobs --job-id
    aws logs

Logs are available in CloudWatch/LogGroups

# mounting/unmounting s3 on local computer

    s3fs mybucket /path/to/mountpoint
    fusermount -u /path/to/mountpoint
