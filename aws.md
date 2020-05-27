    aws batch describe-jobs --job-id
    aws logs

Logs are available in CloudWatch/LogGroups

Mounting/unmounting s3 on local computer

    s3fs mybucket /path/to/mountpoint
    fusermount -u /path/to/mountpoint

Get most recent log

    aws logs describe-log-streams \
    --log-group-name /aws/log/group/name \
    --max-items 1 \
    --order-by LastEventTime \
    --descending \
    --query logStreams[].logStreamName \
    --output text | \
    head -n 1
