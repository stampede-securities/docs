# Hosting User Files in S3 Buckets

## General Background

### S3 Storage

In order to store user content such as photos, pdfs, etc., we upload the files to an AWS S3 bucket, which is Amazon's service for basic cloud storage. Since bucket names cannot contain uppercase letters or underscores, files can be accessed by using their name as an endpoint with the url "https://yourbucketname.s3.amazonaws.com." There are [`more rules and limitations`](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html) for S3 buckets, but the steps in this guide will comply with AWS standards at the time of writing. The only possible issue would be if a bucket name exceeded 63 characters with an extremely long bucket name, but that probably won't happen.

### Secure User Upload

To prevent users from having unfettered upload access to our buckets, we utilize [`pre-signed upload urls`](https://docs.aws.amazon.com/AmazonS3/latest/dev/PresignedUrlUploadObject.html). By uploading an access key id and a secret access key to the api server, the api can create a temporary signed url for a requested file and filetype, which allows the user to perform a PUT action for a specified bucket. This process allows us to control user uploads and restrict authorization instead of granting the public access to put anything in the bucket.

### Secure User Reads

Currently, for projects such as The Key and Joyfull, although the upload process is restricted, GET access to the bucket is public. For projects where the only uploaded files are public profile images or other content that anyone is allowed to view, public read access does not pose a major security threat. When signing the upload URL, the api sets the endpoint for the uploaded file to be the requested file name, prepended with a uuidv4. Anyone can retrieve the bucket url from the file src in the html inspector, but this uuidv4 will make it difficult for an unauthorized party to access another user's files by guessing the file name. However, if the privacy of files is an important concern, **security through obscurity** is not an optimal solution.

If a project needs to keep files secure, it should also have a private files bucket, which does not have public GET access. (AWS supports encrypted buckets, but the exact setup of the private bucket will vary depending on the desired balance between performance and security). At the most general level of this setup, instead of allowing users to directly access a bucket's files, the api would accept a request from the user for a certain file, check authorization, personally retrieve the file, and then forward it to the user. This allows the bucket to be completely private except to the IAM user that is created under the DEV AWS account.

## Setting Up File Buckets on AWS

### Creating Credentials for URL signing

- Log in to the AWS Console and navigate to the IAM service
- Go to "Policies" in the sidebar and then "Create Policy"
- Select "JSON" and then enter this policy, substituting project for your project name

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::project-files-staging"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::project-files-staging/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::project-files-production"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::project-files-production/*"
            ]
        }
    ]
}
```

This creates a policy which can be granted to a user, allowing them to list the contents of your -files-production and -files-staging buckets, read those files, and add new files. If you are adding additional buckets for secure files you can create a new policy for secure file access, copying the rules for listing and getting/reading, and replacing the name with "project-files-secure" or whatever you will call your bucket(s).

- Click "Review Policy" to see the new rules, and enter the name ProjectFileBucketsAccess, replacing "Project" with your project name. Confirm your new policy.
- Navigate to the "Users" tab in the sidebar, and click the button to add a new user
- Name the user "project-files-access," replacing "project" with yours
- Check programmatic access (this user will only be accessed through the api)
- Click "Attach existing policies directly" and search for the policy you just created
- Add the policy, click "review," then "create user"
- **These credentials will only be displayed once**, so it is crucial to save the credentials.csv and keep it in a safe location where it won't be lost or compromised. **Rename to project-files-access-credentials.csv**, so that the file will not be mistaken.

### Creating the Content Buckets

Repeat these steps for any amount of file buckets that you need to create (i.e. project-files-staging, project-files-production, secure buckets)

- Navigate to the S3 service and click "Create Bucket"
- Enter a DNS compliant name, select N. Virginia (us-east-1), and click "Next"
- Check any desired configuration options (none needed for standard public GET buckets) and click "Next"
  - See AWS docs for the [`encryption features`](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html) that meet your public security needs
  - [`Versioning`](https://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html) will store multiple versions of a file with the same name using an obscure ID instead of overwriting the original
- If you are creating a standard public bucket, click "Manage public permissions" and select "Grant public read access to this bucket" _Please note the security concerns in "Secure User Reads" before selecting this option_

### Configure CORS for File Uploads

- For every bucket you created, click on the bucket name and navigate to "Permissions"
- Click "CORS Configuration," and click on the input box
- Copy and paste this xml configuration

```
<?xml version="1.0" encoding="UTF-8"?>
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
<CORSRule>
    <AllowedOrigin>*</AllowedOrigin>
    <AllowedMethod>GET</AllowedMethod>
    <AllowedMethod>PUT</AllowedMethod>
    <MaxAgeSeconds>3000</MaxAgeSeconds>
    <AllowedHeader>*</AllowedHeader>
</CORSRule>
</CORSConfiguration>
```

This is the same policy as default except it also allows the PUT method. **File uploads will not work if this is not specified.**

## Configuring File Uploads in code

To configure the s3 software development kit (aws-sdk), this package must be installed in the api, credentials and the bucket name must be imported, and the s3 client must be started.

- install the aws-sdk: `npm i aws-sdk`
- import credentials and bucket name from environment variables into the config/index.js

```javascript
module.exports = {
...
  S3: {
    accessKeyId: process.env.AWS_ACCESS_KEY_ID || null,
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY || null,
    s3Bucket: process.env.S3_BUCKET || null,
    region: process.env.AWS_S3_REGION || 'us-east-1',
  }
...
```

- set up the s3 client in config/s3.js

```javascript
const config = require("./index");
const AWS = require("aws-sdk");

const { accessKeyId, secretAccessKey, region } = config.S3;
const s3 = new AWS.S3({ accessKeyId, secretAccessKey, region });

module.exports = s3;
```

- add `require('./config/s3')` to your root index.js in the location where other configured apis are added (see thekey-api)

## File Upload Process in Code

The process for a user to upload files is currently as follows:

1. Frontend application requests an upload URL
2. Backend api signs a URL with the required specifications
3. Frontend receives the URL and uses it to upload their file
4. Frontend parses the returned upload URL to get the filename with uuid, adds it to the base bucket url, and then sends it back to the backend to confirm upload
5. Backend stores the display url in the database for user viewing of the file

Questions:

- Could 4 be handled in the backend with the generated filename? Would the backend be able to verify if the file was uploaded and url is valid?
