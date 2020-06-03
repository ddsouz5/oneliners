    aws batch describe-jobs --jobs
    aws logs

Logs are available in CloudWatch/LogGroups

Mounting/unmounting s3 on local computer

    s3fs mybucket /path/to/mountpoint
    fusermount -u /path/to/mountpoint

Get most recent log stream name

    aws logs describe-log-streams \
    --log-group-name /aws/log/group/name \
    --max-items 1 \
    --order-by LastEventTime \
    --descending \
    --query logStreams[].logStreamName \
    --output text | \
    head -n 1

Get log output from log stream

    aws logs get-log-events \
    --log-group-name /aws/batch/job \
    --log-stream-name logStreamName
    
[Batch genomics on AWS](https://aws.amazon.com/blogs/compute/building-high-throughput-genomics-batch-workflows-on-aws-introduction-part-1-of-4/)

