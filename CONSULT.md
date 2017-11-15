.env files should be in more secure .env like s3 bucket then you need to
uncomment it in ./build script

config.yml, config2.yml => this is a file you use to configure your private docker hub.
Tricky thing is you ABSOLUTELY need to match policy for accessing S3 or else
you'll get cryptic errors.

Both file tested and working.

S3_Storage_Drivers_policy => Needs to be applied to aws USERS section to
specific user.
