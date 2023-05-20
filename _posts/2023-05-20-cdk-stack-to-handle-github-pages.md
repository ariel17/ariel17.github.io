---
layout: post
title: CDK stack to handle GitHub pages
date: 2023-05-20 00:11 -0300
---

The following CDK stack configuration is required to configure AWS DNS servers
in order to resolve an static site on GitHub Pages:

```typescript
import * as cdk from 'aws-cdk-lib';
import { Construct } from 'constructs';
import * as route53 from 'aws-cdk-lib/aws-route53';


export const domainName = 'mydomain.org';

export class WebStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    const zone = new route53.PublicHostedZone(this, 'web-hosted-zone', {
      zoneName: domainName,
    });

    new route53.ARecord(this, 'a-record', {
      zone: zone,
      recordName: domainName,
      target: route53.RecordTarget.fromIpAddresses(
        '185.199.108.153',
        '185.199.109.153',
        '185.199.110.153',
        '185.199.111.153',
      ),
    });

    new cdk.CfnOutput(this, 'ns-servers', {
      description: 'NS servers',
      value: cdk.Fn.join(',', zone.hostedZoneNameServers || []),
    });
  }
}
```
