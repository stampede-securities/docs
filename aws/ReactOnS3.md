# Serverless React Hosting on S3

## General Background

Since React is a JavaScript library the frontend applications that we build are entirely static, meaning that they are evaluated entirely in the client side. Running `npm run build` (an alias for `react-scripts build`) produces a production build of our React projects in the directory `build/`. This directory contains everything needed to run the frontend portion of our applications, so by uploading those files to an S3 bucket (Amazon's simple cloud storage hosting) we can minimize costs in serving websites by avoiding unnecessary virtual machines on Heroku. **NOTE: AWS limits accounts to 100 buckets each (must contact if we approach this limit).**

With minimal configuration, S3 buckets can serve static websites, and with only a bit more configuration, we can leverage CloudFront (Amazon's content distribution network service) to allow for secure hosting of sites over HTTPS.

## Steps for Basic React Hosting

### Creating a Deploy User

- Navigate to the IAM service in the AWS console
- Go to the users tab and click "Add user"
- Enter "project-deploy-user" in the User name
- Select "Programmatic access" for access type and hit next
- Click "review" then "create user"
- **These credentials will only be displayed once**, so it is crucial to save the credentials.csv and keep it in a safe location where it won't be lost or compromised. **Rename to project-deploy-user-credentials.csv**, so that the file will not be mistaken.
- Click "close" then click on the user that was just created
- Copy the "User ARN" listed under "Summary"
  - This will be needed for the S3 bucket policy to grant the user deploy access

### Creating a Bucket

- Navigate to the S3 service in the AWS console
- Click "Create bucket"
- Enter a DNS-compliant name for the bucket **NOTE: if you are hosting without CloudFront, the bucket must be named exactly as it will be routed (i.e. subdomain.domain.com)**
- Select your region (N. Virginia for us-east-1)
  - This is typically the closest location, although CloudFront handles efficient distribution to edge locations, so this decision is less important when using a CDN. However, it is best to keep the bucket and CDN in the same region.
- You can configure your bucket with versioning to store older versions of files instead of overwriting, but this is not necessary. Configure as desired, then hit "next"
- To serve as a public website, you must select "Grant public read acces to this bucket" from the "Manage public permissions" dropdown menu. Click "next"

### Configuring the Bucket

- "Create bucket"
- Click the bucket and navigate to the "Properties tab"
- Click on the card that says "Static website hosting"
- Select "Use this bucket to host a website"
- Enter index.html for "Index document" and "Error document"
  - **If you are using react-snapshot in the build script this will be 200.html instead**
- Save the options
- Navigate to "Permissions" and then click "Bucket Policy"
- Copy this policy into the editor, replacing "nameofyourbucket" with your bucket name, and pasting the IAM user ARN where specified

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ReadAccess",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::nameofyourbucket/*"
        },
        {
            "Sid": "ListWriteDeleteAccess",
            "Effect": "Allow",
            "Principal": {
                "AWS": "(paste your full user ARN here)"
            },
            "Action": [
                "s3:ListBucket",
                "s3:PutObject",
                "s3:DeleteObject"
            ],
            "Resource": [
                "arn:aws:s3:::nameofyourbucket/*",
                "arn:aws:s3:::nameofyourbucket"
            ]
        }
    ]
}
```
