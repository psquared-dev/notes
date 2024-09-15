how rto create defaultr vpc
create key-par without cration isntance
what's the ip of the default vpc 172.31.0.0/16
Is S3 regional? yes
Is EC2 regional? yes
Bucket name must be unique in a region or acroess all aws account? all aws account
What's the max size of object we can upload to s3? 5TB
How many buckets can we create in s3? 100 soft limit, 1000 hard limit
CAn we mount s3 bucket on ec2? No, it is a block storage, it is used for storing unstrcuted data only.
Does s3 have hirarchy like filesystem? No, evetythinh is flat
Waht the format of arn? arn:aws:service_name:region:account_no:resource_name
What is high availibity? minimize outages
What is fault tolerance? operate through faults
what is disaster recoivery? used when these (HA and FT) don't work
You an have 5000 IAM user per account
IAM user can be a member of 10 groups
groups can't be nested
group can't be used to login
resource policy can reference IAM roles and IAM users but not group
Can we connect subnet to multiple route table? No
Can we connect route table to multiple subnets? yes
Nat gateway works with IPv6? No