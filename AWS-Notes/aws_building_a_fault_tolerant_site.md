# Building a Fault-Tolerant Site
First, we're going to create our S3 buckets that will hold the code and assets for the site. We then create a CloudFront Distribution.

#### CloudFront Distribution setup
- Set the `Origin Domain Name` to the bucket that contains our media assets.
- For this example, we can leave the rest of the fields as default.
---

We then go to the VPC dashboard to create a security group to be used with all the resources needed to create the site. Under `Security Groups`, click `Create security group`. We will create 2 security groups: one for the webserver and the other for the databases. When creating our webserver security group, modify the `Inbound Rules` to allow HTTP and SSH access at port 80 and port 22 and set the source to `0.0.0.0/0` to allow all machines to connect to the site. When creating the database security group, modify the inbound rules to allow MySQL/Aurora at port 3306 and set the source as your webserver security group ID. Then go to the `RDS` dashboard to begin creating our database. When creating the DB, select the `Additional connectivity configuration` dropdown and choose the security group that we created for our database. We also have to name our DB under `Additional configuration`. From there, our database is ready to create.

---
Head over to IAM and create a new Role, if needed, to allow EC2 instances to have full access to S3. Finally, head over to EC2 to create our instance. Under `Instance Role`, set it to the S3 access role we just created (search the name that you assigned to the role. Under `additional details`, you can create a bootstrap script that will run on initialization of this instance. Below we have an example of one you can copy and paste. 
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
