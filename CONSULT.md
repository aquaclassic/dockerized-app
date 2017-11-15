.env files should be in more secure .env like s3 bucket then you need to
uncomment it in ./build script

config.yml, config2.yml => this is a file you use to configure your private docker hub.
Tricky thing is you ABSOLUTELY need to match policy for accessing S3 or else
you'll get cryptic errors.

Both file tested and working.

S3_Storage_Drivers_policy => Needs to be applied to aws USERS section to
specific user.

1. Configure .aws credentials on jenkins server so jenkins can access s3
   buckets, with python and aws-ci and python 2.7
   user ubuntu is doing the installs
   ```
   https://stackoverflow.com/questions/14547631/python-locale-error-unsupported-locale-setting
   export LC_ALL="en_US.UTF-8"
   export LC_CTYPE="en_US.UTF-8"
   sudo dpkg-reconfigure locales
   sudo apt-get install -y python2.7 python-pip
   sudo pip install -U pip
   sudo pip install virtualenv
   sudo su jenkins
   cd
   virtualenv .venv # change to that env
   source .venv/bin/activate
   pip install awscli
   aws configure => adding userkey and secret access key
   # you should have .aws/credentials and .aws/config in that virtual env
   ```
2. Configure policy so you can only download env file from `dmc-secrets2`  

```
https://awspolicygen.s3.amazonaws.com/policygen.html
S3 policy: GetObject, GebObjectAcl,
ListBucket, 
ARN all: arn:aws:s3:::dmc-secrets2/*
ARN all: arn:aws:s3:::dmc-secrets2/test.txt
```
3. Test: `aws s3 cp s3://dmc-secrets2/test.txt ./test.txt` # you must be in that
   env
9. 
