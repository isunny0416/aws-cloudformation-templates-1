[**English**](README.md) / 日本語

# AWSCloudFormationTemplates/web-servers
![Build Status](https://codebuild.ap-northeast-1.amazonaws.com/badges?uuid=eyJlbmNyeXB0ZWREYXRhIjoiT1o3djE0RFpweWErRDl6SkpwTGsySVJKbWk0ajhreUlEaXAvTHh3ZzdaS2wzNVR5V1hpZkZRRVRtcFIvNncydWdad2w4TG9MRVMzVGFvMlZKY2RNYUowPSIsIml2UGFyYW1ldGVyU3BlYyI6Ik0vOGVWdGFEWTlyYVdDZUwiLCJtYXRlcmlhbFNldFNlcmlhbCI6MX0%3D&branch=master)
![GitHub](https://img.shields.io/github/license/eijikominami/aws-cloudformation-templates)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/eijikominami/aws-cloudformation-templates)
 
``AWSCloudFormationTemplates/web-servers`` は、 ``Network Load Balancer``や ``VPC`` 、 ``EC2`` インスタンスなどの **EC2で構成されたWebサイトホスティング** に関連するAWSサービスを設定します。

## TL;DR

以下のボタンをクリックすることで、この **CloudFormationをデプロイ** することが可能です。

[![cloudformation-launch-stack](../images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?stackName=WebServers&templateURL=https://eijikominami.s3-ap-northeast-1.amazonaws.com/aws-cloudformation-templates/web-servers/template.yaml) 

以下のボタンから、個別のAWSサービスを有効化することも可能です。

| Services | Launchers |
| --- | --- |
| Data Lifecycle Manager | [![cloudformation-launch-stack](https://raw.githubusercontent.com/eijikominami/aws-cloudformation-templates/master/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?stackName=DataLifecycleManager&templateURL=https://eijikominami.s3-ap-northeast-1.amazonaws.com/aws-cloudformation-templates/security/dlm.yaml&param_LogicalNamePrefix=DataLifecycleManager) |
| Systems Manager | [![cloudformation-launch-stack](https://raw.githubusercontent.com/eijikominami/aws-cloudformation-templates/master/images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?stackName=SystemsManager&templateURL=https://eijikominami.s3-ap-northeast-1.amazonaws.com/aws-cloudformation-templates/security/ssm.yaml&param_LogicalNamePrefix=SystemsManager) |

## アーキテクチャ

このテンプレートが作成するAWSリソースのアーキテクチャ図は、以下の通りです。

![](../images/architecture-web-servers.png)

## デプロイ

以下のコマンドを実行することで、CloudFormationをデプロイすることが可能です。

```bash
aws cloudformation deploy --template-file template.yaml --stack-name WebServers --capabilities CAPABILITY_NAMED_IAM
```

デプロイ時に、以下のパラメータを指定することができます。

| 名前 | タイプ | デフォルト値 | 必須 | 詳細 |
| --- | --- | --- | --- | --- |
| AutoScalingDesiredCapacity | Number | 1 | ○ | | 
| AutoScalingMaxSize | Number | 1 | ○ | |
| AutoScalingLoadBalancerType | None, application, network | None | ○ | 'None'を指定した場合、ELBは作成されません。 |
| EC2DailySnapshotScheduledAt | String | 17:00 | ○ | スナップショット作成時刻 (UTC) |
| EC2ImageId | AWS::EC2::Image::Id | ami-068a6cefc24c301d2 | ○ | Amazon Linux 2 AMI (HVM), SSD Volume Type (64bit x86) |
| EC2InstanceType | String | t3.micro | ○ | | 
| EC2PatchingAt | Number | 3 | ○ | パッチ処理を開始する時刻 |
| EC2KeyName | String | | | 値が指定されない場合は、 **SSHキー** は設定されません。 |
| EC2VolumeSize | Number | 8 | ○ | |
| IgnoreResourceConflicts | Enabled / Diasbled | Disabled | ○ | Enabled に設定された場合、当該のリソースは生成されません。 |
| SSMPatchingAt | Number | 3 | ○ | パッチ適用処理開始時刻 (現地時) |
| SubnetPublicCidrBlockForAz1 | String | 10.0.0.0/24 | ○ | AZ1 の パブリックサブネット |
| SubnetExternalCidrBlockForAz1 | String | 10.0.1.0/24 | ○ | AZ1 の プライベートサブネット |
| SubnetPublicCidrBlockForAz2 | String | 10.0.4.0/24 | ○ | AZ2 の パブリックサブネット |
| SubnetExternalCidrBlockForAz2 | String | 10.0.5.0/24 | ○ | AZ2 の プライベートサブネット |
| WebACL | Enabled / Diasbled | Disabled | ○ | Disabled に設定された場合、AWS WAFは作成されません。 |
| VPCCidrBlock | String | 10.0.0.0/21 | ○ | |

## トラブルシューティング

`AWS-GatherSoftwareInventory` を含むSSM State Manager の関連付けが既に存在する場合、このテンプレートは失敗します。`IgnoreResourceConflicts` オプションを Enabled に設定してこのテンプレートを実行してください。