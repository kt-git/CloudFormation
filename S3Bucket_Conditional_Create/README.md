<U><B>S3Bucket Conditional Creation Implementor's Notes</B></U>
<BR><BR>
To use this, you can take two paths which should work:
<BR>
1) Implement (a likely slightly altered derivative of) this template as a Sub-Stack in a Nested-Stack model.
2) Pull out just the lambda and the Custom Resource, and replace references to the Stack Parameters that I used and replace with your own (or with fixed settings).  <I>Just remember this isn't a drop-in replacement for AWS::S3::Bucket, instead of using !Ref to the S3 Bucket to get back it's name, you need to replace those with referenes to !GetAtt \<ObjectName\>.Name instead.</I>
