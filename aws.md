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
    
Kill job 

    aws batch terminate-job \
    --reason "wrong submission" \
    --job-id 0f0100bf-9565-4b67-0aaf-f2a01fefbe16
 
Batch genomics on AWS

   * https://aws.amazon.com/blogs/compute/building-high-throughput-genomics-batch-workflows-on-aws-introduction-part-1-of-4/)

Cross Bucket transfer

   * <https://aws.amazon.com/premiumsupport/knowledge-center/copy-s3-objects-account/>
   * <https://docs.aws.amazon.com/AmazonS3/latest/dev/example-walkthroughs-managing-access-example2.html>
   * <https://blog.vizuri.com/how-to-copy/move-objects-from-one-s3-bucket-to-another-between-aws-accounts>
   * <https://medium.com/tensult/copy-s3-bucket-objects-across-aws-accounts-e46c15c4b9e1>

Docker modify existing image

   * https://bobcares.com/blog/edit-docker-image/
   * <https://gist.github.com/glamp/74188691c91d52770807>

Sharing files from S3 using inCognito

   * https://blog.powerupcloud.com/share-files-securely-over-internet-using-aws-cognito-and-s3-126811991d23

S3 general

   * https://medium.com/future-vision/the-birth-of-an-object-on-aws-813124fa7a80

boto3 aws multipart upload

   * https://gist.github.com/teasherm/bb73f21ed2f3b46bc1c2ca48ec2c1cf5
   * https://boto3.amazonaws.com/v1/documentation/api/latest/guide/s3.html
   
get size of buckets

    aws s3 ls --summarize --human-readable --recursive s3://bucket-name/directory

Recursively change storage class on bucket using aws cli

   * https://github.com/aws/aws-cli/issues/2056
   
    aws s3 cp s3://knapp-standard-ia s3://knapp-standard-ia --recursive --storage-class STANDARD_IA

Tag s3 object boto3

    def tag_s3_obj(s3_path=None,tag_key=None,tag_value=None):
    '''Tag s3 object
    '''
    client = boto3.client('s3')

    bucket = s3_path.split('/')[2]
    key = '/'.join(s3_path.split('/')[3:])
    
    response = client.put_object_tagging(
    Bucket=bucket,
    Key=key,
    Tagging={
        'TagSet': [
            {
                'Key': tag_key,
                'Value': tag_value
            },
        ]
    }
    )
    print('Tagging {}'.format(s3_path))
    return response
