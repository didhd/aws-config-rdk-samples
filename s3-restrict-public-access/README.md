# Warning!!! This rule requires an additional step to use.
Prior to rule deployment additional libraries must be added to make this rule function as it requires versions of BOTO3 and BOTOCORE greater than what can be currently supported by AWS lambda.

1. Change directory to the parent folder of s3-restrict-public-access
2. Create newboto directory

     ```   mkdir s3-restrict-public-access/newboto ```
3. Add current libraries to s3-restrict-public-access/newboto folder

```pip3 install boto3 botocore urllib3 --no-deps --target='s3-restrict-public-access/newboto/'```

4. Deploy as normal

     ```    rdk deploy s3-restrict-public-access ```