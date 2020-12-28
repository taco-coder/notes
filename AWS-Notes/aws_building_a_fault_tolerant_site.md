# Building a Fault-Tolerant Site
First, we're going to create our S3 buckets that will hold the code and assets for the site. We then create a CloudFront Distribution.

#### CloudFront Distribution setup
- Set the `Origin Domain Name` to the bucket that contains our media assets.
- For this example, we can leave the rest of the fields as default.
---

We then go to the VPC dashboard to create a security group to be used with all the resources needed to create the site. Under `Security Groups`, click `Create security group`. We will create 2 security groups: one for the webserver and the other for the databases. When creating our webserver security group, modify the `Inbound Rules` to allow HTTP and SSH access at port 80 and port 22 and set the source to `0.0.0.0/0` to allow all machines to connect to the site. When creating the database security group, modify the inbound rules to allow MySQL/Aurora at port 3306 and set the source as your webserver security group ID. Then go to the `RDS` dashboard to begin creating our database. When creating the DB, select the `Additional connectivity configuration` dropdown and choose the security group that we created for our database. We also have to name our DB under `Additional configuration`. From there, our database is ready to create.

---
Head over to IAM and create a new Role, if needed, to allow EC2 instances to have full access to S3. Finally, head over to EC2 to create our instance. Under `Instance Role`, set it to the S3 access role we just created (search the name that you assigned to the role. Under `advanced details`, you can create a bootstrap script that will run on initialization of this instance. Below we have an example of one you can copy and paste. 
```
#!/bin/bash
yum update -y
yum install httpd php php-mysql -y
cd /var/www/html
echo "healthy" > healthy.html
wget https://wordpress.org/wordpress-5.1.1.tar.gz
tar -xzf wordpress-5.1.1.tar.gz
cp -r wordpress/* /var/www/html/
rm -rf wordpress
rm -rf wordpress-5.1.1.tar.gz
chmod -R 755 wp-content
chown -R apache:apache wp-content
wget https://s3.amazonaws.com/bucketforwordpresslab-donotdelete/htaccess.txt
mv htaccess.txt .htaccess
chkconfig httpd on
service httpd start
```
This script updates the instance, installs php and apache, and then creates a new html file in our directory `/var/www/html`. It then downloads and installs Wordpress and gets access to S3 buckets. It renames the htaccess file to be hidden and the starts the apache service. 
---
When we travel to our public IP, we'll see the Wordpress setup prompt. Our `Database Host` should be changed from `localhost` to our DB endpoint found in the RDS DB menu. From there, you can follow the steps to create our new Wordpress site.

When connected to the EC2 instance over SSH, to have any user uploaded images automatically backed up to an S3 bucket run `aws s3 cp --recursive /var/www/html/wp-content/uploads s3://[YOUR ASSETS BUCKET NAME HERE]`.

For redundancy we can also copy our code and all our content to an S3 bucket that way if we lose an EC2 instance, we can run `aws s3 cp --recursive /var/www/html s3://[YOUR CODE BUCKET NAME HERE]`.
To allow URL rewrites we need to modify .htaccess and update the Cloudfront URL in the rewriterule line to our URL for our Cloudfront distribution we created. After the modification, we need to sync our S3 bucket with whats currently in `/var/www/html`.
`aws s3 sync /var/www/html s3://[YOUR CODE BUCKET NAME HERE]`
---
We need to allow URL rewrites in Apache. 
`cd /etc/httpd/conf`

Edit the `httpd.conf` file to allow URL rewrites. We need to scroll down until we see `AllowOverride None` and change it to `All`. This will allow us to rewrite our URLs using the Cloudfront distribution and not our public IP address. Modify our assets bucket to be publicly viewable under the bucket policy in the bucket permissions in S3.
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": [
        "s3:GetObject"
        ],
      "Resource": [
        "arn:aws:s3:::BUCKET_NAME/*"
        ]
    }
  ]
}
```

