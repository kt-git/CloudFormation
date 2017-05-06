<U><B>S3Bucket Conditional Create</B></U>
<BR><BR>
This is a reference implementation of how to do conditional S3 Bucket re-use or creation using a Cloudformation Custom Resource and a Lambda function.  This allows a Cloudformation template to support some unique use cases which typically cause failures or errors when implementing S3 Buckets using AWS::S3::Bucket, or require manual intervention in use cases where you don't want or need manual intervention.  What gave me the impetus to make these were our desire to generate buckets for use in a Cloudformation-generated CI/CD CodePipeline that uses S3 Buckets as both artifact repositories and as Sources to start a Pipeline off.  It's utility is broader than this, so here are some things this implementation does that many of you may well need right now:
<UL>
<BR>
<LI><B>Creation:</B> If passed a bucket which already exists, re-uses that bucket and makes it's ARN/Name available to other parts of the Cloudformation template.  If the bucket doesn't exist, create the bucket, and if so specified in the template, turn versioning on for the bucket.
<BR><BR>
<LI><B>Deletion:</B> Support purging all contents (both versioned and non-versioned) of an S3 Bucket at the time of Deletion, so we can cleanly dispose of any buckets we create in our template at the time of Stack Creation, even if they have contents added to them later.  In normal scenarios using AWS::S3::Bucket, this causes a FAILURE on the S3 Bucket resource upon Stack Deletion, because an S3 Bucket Delete API call will only succeed on an EMPTY bucket.  We make it possible to have both clean Stack Deletes when your use case is that the bucket is desired to go away at Stack Deletion time, data and all (perfect for our CodePipeline use case.)
<BR><BR>
<LI><B>Updates:</B> If the bucket name is changed between updates in the CloudFormation template, support deleting the old bucket name and creating the bucket name following the same guidelines as above.  This doesn't move the data around, but for use cases where the data is easily re-populated, this is a suitable, and likely desireable, implementation choice (again, perfect for our CodePipeline use case.)
</UL><BR><BR>
<U><B>Implementor's Notes</B></U>
<BR><BR>
To use this, you can take two paths which should work:
<BR>
1) Implement (a likely slightly altered derivative of) this template as a Sub-Stack in a Nested-Stack model.
2) Pull out just the lambda and the Custom Resource, and replace references to the Stack Parameters that I used and replace with your own (or with fixed settings).  <I>Just remember this isn't a drop-in replacement for AWS::S3::Bucket, instead of using !Ref to the S3 Bucket to get back it's name, you need to replace those with referenes to !GetAtt \<ObjectName\>.Name instead.</I>
