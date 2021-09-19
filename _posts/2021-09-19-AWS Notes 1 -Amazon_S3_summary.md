---
layout: post
title:  " AWS Notes #1 Amazon S3 Summary"
author: baskar
image: assets/images/S3.jfif
categories: [AWS]
tags: [AWS]
featured: false
hidden: false
---



**S3**

- Object storage, serverless, pay as you go

**Anti-Patterns**

- lots of small files
- no file locking
- search features

**Classes**

- Standard
- Intelligent Tiering
- Standard-IA
- S3 Glacier
- Glacier Deep Archive

**Replication**

- Cross and same region replication
- Reduce latency, securitty, recovery

**Notifications**

- Notifications on various S3 actions
- Can trigger Lambda or other things
- Send to S3, SNS, SQS, etc
- Take seconds up to a couple minutes
- Must enable versioning to ensure multiple-write notifications

**CloudWatch Events**

- Need to enable CloudTrail logs for S3 actions on object-level calls

**Performance**

- Will scale to high request rate, 100**-**200ms
- 3500 PUT/COPY/POST/DELETE AND 5500 GET/HEAD req per second per prefix in a bucket
- No limit on prefixes
- Need to shard across prefixes for maximum performance
- **Multi-part upload** for files over 5GB, recommended for files for 100MB

Speeds up uploads by doing in parallel

**S3 Transfer Acceleration** (upload only)

Increase uploads, can be combined with multi-part upload

- **S3 Byte-Range Fetches**

`   `Parallelize GETs

`  	   `Speed up downloads

`         `Can resume sections

`         `Only retrieve first part of files

- **S3/Glacier Select**

`   `Retrieve data using SQL by performing server side filtering

`   `less network transfer, less CPU cost client-side







**Analytics**

- Data lake con concepts          - Athenam Redshidt spectrum,Quicksight
- IOT streaming data repository   - Kinesis Firehose
- Machine learning and AI storage – Recognition,Lex,MxNet
- Storage class analysis          - S3 Management analysis


**S3 Encrytion at rest**

- SSE-S2
- SSE-C
- SSE-KMS
- Client side


**S3 Nifty Tricks**

- Transfer acceleration
- Requestor Paya
- Tags
- Events
- Statis web hosting
- BitTorrent




**AWS white paper – Perfomance guidelines for Amazon S3**

[**https://docs.aws.amazon.com/whitepapers/latest/s3-optimizing-performance-best-practices**](https://docs.aws.amazon.com/whitepapers/latest/s3-optimizing-performance-best-practices)

**Summary:**

1. **Measure Performance**
- **Evaluate different amazon EC2 Instance types**
- **Check DNS look up time,latency,data transfer speed using HTTP analysis tools**

2. **Scale storage containers horizontally**
- **Spreading requests accross many conenctions**
- **S3 doesnt have any limits on number of connections made to your bucket**

3. **Use Byte-Range Fetches**
- **Use range HTTP header in tthe GET object request and use concurrent connections to S3**

4. **Combine S3 and EC2 in the same AWS region**

5. **Use Amazon S3 tranfer acceleration to minimize latency**

6. **Use latest versioin of AWS SDKs which provides Transfer Manager which automates horizontal scaling connections**

7. **Use caching for frequently accessed content using Amazon cloudfront,Amazon Elasticache for REDIS,AWS elemental media store.**
8. **Timeouts and retries for latency sentive applications**
   - **AWS SDK implements automatic retry logic while receiving HTTP 503 error using exponential backoff.**
