<img width="1239" height="889" alt="Screenshot 2026-03-12 at 00 06 37" src="https://github.com/user-attachments/assets/18365f35-e928-42ea-9c66-fa564a7c64f4" />
**Website hosting using Jenkins**

1. Upload file to S3
2. Download it back to the intance and then deploy the application.
3. The github repo being used: https://github.com/devRjj/jenkins-demo2.git

Steps are as follows:
1. Created an IAM role with S3FullAccess.
2. Attached it to our EC2 instance.
3. Running the jenkins.
4. Creating the job with this script:
```
#!/bin/bash
set -e

BUCKET="devrjj-ecommerce-demo-2026-03-11"
REGION="ap-south-1"
WEB_DIR="/var/www/html"

echo "=== Deploy to httpd ==="

# Install httpd if missing
if ! rpm -q httpd > /dev/null 2>&1; then
    echo "Installing httpd..."
    sudo dnf install -y httpd
fi

# Start/enable httpd
sudo systemctl enable httpd --now
sudo systemctl status httpd

# Create web dir
sudo mkdir -p $WEB_DIR
sudo chown -R apache:apache $WEB_DIR
sudo chmod -R 777 $WEB_DIR

# Download from S3
echo "Downloading from S3..."
aws s3 cp s3://$BUCKET/index.html $WEB_DIR/index.html

# Fix permissions
sudo chown apache:apache $WEB_DIR/index.html
sudo chmod -R 777 $WEB_DIR/index.html

systemctl start httpd
systemctl enable httpd

echo "✅ Deployed to httpd!"
echo "Access: http://13.127.195.233"  # Your EC2 IP
echo "S3 backup: http://$BUCKET.s3-website-$REGION.amazonaws.com"
```
