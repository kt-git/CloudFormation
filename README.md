# CloudFormation
AWS Cloudformation Examples and Reference Implementations

## <U>Reference Implementations</U>:
<UL>
<LI><B>S3Bucket_Conditional_Create:</B> A reference implementation of how to do conditional S3 Bucket re-use or creation using a Cloudformation Custom Resource and a Lambda function.  This allows a Cloudformation template to support some unique use cases which typically cause failures or errors when implementing S3 Buckets using AWS::S3::Bucket, or require manual intervention in use cases where you don't want or need manual intervention.  What gave me the impetus to make these were our desire to generate buckets for use in a Cloudformation-generated CI/CD CodePipeline that uses S3 Buckets as both artifact repositories and as Sources to start a Pipeline off.  It's utility is broader than this, so here are some things this implementation does that many of you may well need right now:
<UL>
<BR>
<LI><B>Creation:</B> If passed a bucket which already exists, re-uses that bucket and makes it's ARN/Name available to other parts of the Cloudformation template.  If the bucket doesn't exist, create the bucket, and if so specified in the template, turn versioning on for the bucket.
<BR>
<LI><B>Deletion:</B> Support purging all contents (both versioned and non-versioned) of an S3 Bucket at the time of Deletion, so we can cleanly dispose of any buckets we create in our template at the time of Stack Creation, even if they have contents added to them later.  In normal scenarios using AWS::S3::Bucket, this causes a FAILURE on the S3 Bucket resource upon Stack Deletion, because an S3 Bucket Delete API call will only succeed on an EMPTY bucket.  We make it possible to have both clean Stack Deletes when your use case is that the bucket is desired to go away at Stack Deletion time, data and all (perfect for our CodePipeline use case.)
<BR>
<LI><B>Updates:</B> If the bucket name is changed between updates in the CloudFormation template, support deleting the old bucket name and creating the bucket name following the same guidelines as above.  This doesn't move the data around, but for use cases where the data is easily re-populated, this is a suitable, and likely desireable, implementation choice (again, perfect for our CodePipeline use case.)
