
## AWS Configuration

You need to have the below environment variable set for Terraform to be able to access your AWS account.

```bash
$ export AWS_ACCESS_KEY_ID=(your access key id)
$ export AWS_SECRET_ACCESS_KEY=(your secret access key)
```

With these set you can run your terraform init, apply etc commands to manage AWS resources.