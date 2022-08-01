# 設定 AWS CLI
```
01 # aws configure
    AWS Access Key ID [****************U47V]:   存取金鑰 ID
    AWS Secret Access Key [****************Yq2/]:   私密存取金鑰
    Default region name [ap-northeast-1]:   AWS 區域
    Default output format [json]:   輸出格式

02 # aws configservice put-aggregation-authorization --authorized-account-id  285167715064 --authorized-aws-region ap-northeast-1
    {
        "AggregationAuthorization": {
            "AggregationAuthorizationArn": "arn:aws:config:ap-northeast-1:285167715064:aggregation-authorization/285167715064/ap-northeast-1",
            "AuthorizedAccountId": "285167715064",
            "AuthorizedAwsRegion": "ap-northeast-1",
            "CreationTime": "2022-08-01T09:59:02.916000+08:00"
        }
    }
```

## 補充資料
* [存取金鑰 ID 和私密存取金鑰的取得方式](accesskey.md)
* [AWS CLI 組態基礎概念](https://docs.aws.amazon.com/zh_tw/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-creds-create)
* [AWS CLI 快速設定](https://docs.aws.amazon.com/zh_tw/cli/latest/userguide/getting-started-quickstart.html)