# moby-sonarqube-db_backup

## 1.Backup Solution.
We are using a shell script `scripts/docker_vol_backup.sh` to backup sonarqube and DB volumes, then push tar.gz files to AWS S3 Bucket “moby-sonarqube-backup“ 
Note : 
* This script is running as daily cronjob 
* We have S3 bucket lifecycle to retain backups for 15 days.
```
0 1 * * * /home/ec2-user/moby-sonarqube/scripts/docker_vol_backup.sh >> /srv/sonarqube/logfile.log 2>&1
```

## 2.Restore Process.
- Create a new EC2 Instance from using daily snapshot created by  following policy.
```
https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#LifecyclePolicyDetails:policyId=policy-00a910f264295bbf5
```
Set security group `SG-Test-SonarQube` and iam_role `sonar-role`.

- Use following script to restore sonarqube and DB docker volumes. Please pass Backup_Date from “moby-sonarqube-backup“ S3 Bucket.
```
sudo sh scripts/docker_vol_restore.sh
```
