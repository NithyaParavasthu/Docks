# Velero BackUp

### Create a User
We have to Create a user with the following velero policy. and save the user's credentials for further use.
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVolumes",
                "ec2:DescribeSnapshots",
                "ec2:CreateTags",
                "ec2:CreateVolume",
                "ec2:CreateSnapshot",
                "ec2:DeleteSnapshot"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:DeleteObject",
                "s3:PutObject",
                "s3:AbortMultipartUpload",
                "s3:ListMultipartUploadParts"
            ],
            "Resource": [
                "arn:aws:s3:::${BUCKET}/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::${BUCKET}"
            ]
        }
    ]
}
```
### Saving the credentials In Local
Now, Store this credentials in a file called **credentials-velero**.
```
vim credentials-velero
```
Add the credentials in the following formate.
```
[default]
aws_access_key_id=<Aws access key>
aws_secret_access_key=<Aws secret key>
```
### Export this variables
```
BUCKET=<Name of your bucket>
REGION=<region of bucket>
```
## Installing and Backing Up

Now Install Velero with the follwing command.
``` 
velero install     --provider aws     --plugins velero/velero-plugin-for-aws:v1.4.0     --bucket $BUCKET     --backup-location-config region=$REGION     --snapshot-location-config region=$REGION     --secret-file ./credentials-velero --use-restic
```
### Ceating Backup Location 
Create a another backup location in which you need your backup to be stored. This location is usefull when needed backup in another cluster.

First we need to create secret with the credentials to create a backup as shown below
```
kubectl create secret generic -n velero bsl-credentials --from-file=aws=./credentials-velero
```
Now create backup location with the following command. 
```
velero backup-location create <location name>   --provider aws   --bucket $BUCKET   --config region=$REGION   --credential=bsl-credentials=aws
```
### Scheduling Backup

Now we need to schedule our backup so when the backup is created, it would automatically backup on the scheduled time.

Schedule the backup with the follwing command.
```
velero schedule create <schedule name> --schedule="0 0 * * *" --include-namespaces default --storage-location <default/<name of the backup location created>> --snapshot-volumes=true --default-volumes-to-restic true
```
### Creating Backup

Now Create a Backup with the follwing command.
```
velero backup create <backup name>  --from-schedule <schedule name>
```
### Restore Backup

To restore the back-up from the bucket

```
velero restore create <restore name> \
  --from-backup  <backup name>
```



