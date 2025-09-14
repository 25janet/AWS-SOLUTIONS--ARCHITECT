# AWS Academy Challenge Lab: Dynamic Website for the Café

This writeup documents the full challenge lab where we configure an EC2
instance to host a dynamic website for a café, with database and Secrets
Manager integration. It includes not just the *how*, but also the *why*
behind each step.

------------------------------------------------------------------------

## Lab Objectives

-   Launch and configure an EC2 instance running Apache, PHP, and
    MariaDB
-   Attach IAM roles and permissions for accessing AWS Secrets Manager
-   Deploy a dynamic PHP application (the café website)
-   Configure networking and security groups for internet access
-   Package the environment into an AMI and redeploy in another region

------------------------------------------------------------------------

## Step-by-Step Breakdown

### 1. Inspect Environment

-   **Command:** `cat /proc/version`
    -   *Why:* Verify we're on Amazon Linux (RHEL-like) to know
        available packages.
-   **Command:** `sudo httpd -v`, `php --version`
    -   *Why:* Confirm web server (Apache) and PHP are installed but not
        yet running.

### 2. Start Web & Database Services

``` bash
sudo chkconfig httpd on
sudo service httpd start
sudo systemctl enable mariadb
sudo service mariadb start
```

-   *Why:* Ensures Apache and MariaDB start now and also on reboot, so
    the server survives restarts.

### 3. Enable Editing in Cloud9

``` bash
ln -s /var/www/ /home/ec2-user/environment
sudo chown ec2-user:ec2-user /var/www/html
```

-   *Why:* Symbolic link lets Cloud9 access `/var/www/html` where web
    files live. Ownership change allows editing without sudo.

### 4. Test Static Page

-   Add `index.html` with "Hello from the café web server!"
-   Verify at `http://<public-ip>/`
-   *Why:* Confirms security group rules + Apache config are correct.

### 5. Deploy Café App

-   Download/unzip `setup.zip`, `db.zip`, and `cafe.zip`
-   Copy café app into `/var/www/html/cafe`
-   Install AWS PHP SDK (aws.phar)
-   *Why:* Deploys the real web app files beyond static HTML.

### 6. Configure Parameters in Secrets Manager

``` bash
cd ~/environment/setup/
./set-app-parameters.sh
```

-   *Why:* Script stores sensitive values (DB password, endpoint, etc.)
    in AWS Secrets Manager instead of hardcoding them.

### 7. Configure Database

``` bash
cd ~/environment/db/
./set-root-password.sh
./create-db.sh
```

-   *Why:* Creates schema and sample data for café app. DB password
    retrieved via Secrets Manager.

### 8. IAM Role for EC2

-   Attach **CafeRole** to EC2 instance.
-   *Why:* Without this, app cannot call Secrets Manager
    (`AccessDenied`). IAM role provides least-privilege credentials
    automatically.

### 9. Verify Café Website

-   Visit `http://<public-ip>/cafe`
-   Should show menu items from DB.
-   *Why:* Confirms all pieces (web → IAM → Secrets Manager → DB) work
    together.

### 10. Create and Copy AMI

-   From N. Virginia (us-east-1), create an AMI of the instance.
-   Use **Actions → Copy AMI** to Oregon (us-west-2).
-   *Why:* AMIs are region-specific; must copy before reuse.

### 11. Launch in Oregon

-   Launch instance from copied AMI.
-   Name: `ProdCafeServer`
-   Instance type: `t2.small`
-   Security group: Allow **TCP 22, 80** from anywhere.
-   Attach IAM role: `CafeRole`
-   *Why:* Ensures new region instance has same setup and permissions.

### 12. Update Setup Script

-   In Cloud9 (us-east-1), edit `setup/set-app-parameters.sh`:

    ``` bash
    region="us-west-2"
    publicDNS="ec2-xx-xxx-xxx-xxx.us-west-2.compute.amazonaws.com"
    ```

-   Re-run the script to update parameters in Oregon.

-   *Why:* Ensures Secrets Manager and app config point to new region
    and server.

------------------------------------------------------------------------

## Commands Quick Reference

``` bash
# Check versions
cat /proc/version
sudo httpd -v
php --version

# Start services
sudo chkconfig httpd on
sudo service httpd start
sudo systemctl enable mariadb
sudo service mariadb start

# Enable Cloud9 editing
ln -s /var/www/ /home/ec2-user/environment
sudo chown ec2-user:ec2-user /var/www/html

# Deploy café app
wget <setup.zip> && unzip setup.zip
wget <db.zip> && unzip db.zip
wget <cafe.zip> && unzip cafe.zip -d /var/www/html/

# Configure parameters
cd ~/environment/setup/
./set-app-parameters.sh

# Configure DB
cd ~/environment/db/
./set-root-password.sh
./create-db.sh
```

------------------------------------------------------------------------

## Evidence & Screenshot Checklist

For submission, capture screenshots of: 1. `cat /proc/version` output
(Amazon Linux info) 2. `php --version` output 3. Services running:
`service httpd status`, `service mariadb status` 4. Cloud9 file browser
showing `/var/www/html/index.html` 5. Browser showing
`http://<public-ip>/` test page 6. Secrets Manager console with
`/cafe/dbPassword` 7. `mariadb --version` + DB tables 8. EC2 Security
Group showing ports 22 and 80 open 9. Café web page with menu items
loaded 10. AMI list showing created AMI 11. Oregon (us-west-2) EC2
instance `ProdCafeServer` 12. Updated `setup/set-app-parameters.sh` with
region + DNS

------------------------------------------------------------------------

## Example Submission Text

> I successfully configured an EC2 instance in us-east-1 to host the
> café web app with Apache, PHP, and MariaDB. I attached IAM role
> `CafeRole` to enable secure access to AWS Secrets Manager. I deployed
> the app, verified database integration, and confirmed functionality at
> `http://<public-ip>/cafe`. I then created an AMI, copied it to
> us-west-2, and launched `ProdCafeServer` with correct IAM and security
> group settings. Finally, I updated the setup script to point to
> Oregon's region and DNS, and validated the app works there as well.

------------------------------------------------------------------------

## Troubleshooting Notes

-   If `http://<public-ip>/` loads but `/cafe` fails → missing IAM role
    or wrong region in setup script.
-   If database errors → ensure `create-db.sh` ran and Secrets Manager
    has correct password.
-   If AMI not visible in Oregon → must use "Copy AMI" action, as AMIs
    are region-specific.

------------------------------------------------------------------------

## Optional Next Steps

-   Add HTTPS via ACM + Load Balancer
-   Use Auto Scaling group for high availability
-   Store static assets in S3 + CloudFront CDN

------------------------------------------------------------------------