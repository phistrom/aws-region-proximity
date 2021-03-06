# AWS Region Proximity

Print a JSON dictionary where the key is an AWS region code name (us-west-2,
eu-central-1, etc.), and the value is a list of regions in order of their
physical proximity to the region. You may optionally pass in a list of region
code names that you have a copy of the resource in. The results will have the
same keys, but the lists will only contain the regions you specified.

## Why?

Say you have S3 buckets being cross-region-replicated in `us-west-2`,
`eu-central-1`, and `ap-southeast-1` and you want to put a Cloudfront
distribution in front of them. You can use a Lambda@Edge with an Origin Request
trigger to change the origin that Cloudfront will pull a resource from.
Lambda@Edge has the environment variable `AWS_REGION`, which will tell you the
closest region to where your Lambda is executing. You could then use the list
generated by this program to help find the nearest origin that can handle the
request.

For example, a request comes in to the **DFW** edge location. The environment
variable `AWS_REGION` is `"us-east-1"` (the AWS region associated with the edge
location of **DFW**). You don't have a copy of the resource in `us-east-1`, but
you do have a copy in `us-west-2`. So that can be the copy you return (as
opposed to your copy in `eu-central-1` or `ap-southeast-1`).

## Install
```sh
pip install aws-region-proximity
```

## Usage
### Get the Closest Regions for All Regions
```sh
aws-regions
```
### Output:
```json
{
    "us-east-1": [
        "us-east-1",
        "us-east-2",
        "ca-central-1",
        "us-west-2",
        "us-west-1",
        "eu-west-1",
        "eu-west-2",
        "eu-west-3",
        "eu-central-1",
        "eu-north-1",
        "eu-south-1",
        "sa-east-1",
        "ap-northeast-1",
        "me-south-1",
        "cn-north-1",
        "ap-northeast-2",
        "cn-northwest-1",
        "af-south-1",
        "ap-south-1",
        "ap-east-1",
        "ap-southeast-1",
        "ap-southeast-2"
    ],
    ...
    "cn-northwest-1": [
        "cn-northwest-1",
        "cn-north-1",
        "ap-east-1",
        "ap-northeast-2",
        "ap-northeast-1",
        "ap-south-1",
        "ap-southeast-1",
        "me-south-1",
        "eu-north-1",
        "eu-central-1",
        "eu-south-1",
        "eu-west-2",
        "eu-west-3",
        "eu-west-1",
        "ap-southeast-2",
        "us-west-2",
        "us-west-1",
        "ca-central-1",
        "us-east-2",
        "us-east-1",
        "af-south-1",
        "sa-east-1"
    ]
}
```

#### Get Closest Specified Regions per Region
For when you have copies in a limited number of regions and just want to see
your closest available region given some other region.

```sh
aws-regions us-west-2 eu-central-1 ap-southeast-1
```

#### Output
```json
{
    "us-east-1": [
        "us-west-2",
        "eu-central-1",
        "ap-southeast-1"
    ],
    "us-east-2": [
        "us-west-2",
        "eu-central-1",
        "ap-southeast-1"
    ],
    "us-west-1": [
        "us-west-2",
        "eu-central-1",
        "ap-southeast-1"
    ],
    "us-west-2": [
        "us-west-2",
        "eu-central-1",
        "ap-southeast-1"
    ],
    "eu-west-1": [
        "eu-central-1",
        "us-west-2",
        "ap-southeast-1"
    ],
    ...
    "cn-north-1": [
        "ap-southeast-1",
        "eu-central-1",
        "us-west-2"
    ],
    "cn-northwest-1": [
        "ap-southeast-1",
        "eu-central-1",
        "us-west-2"
    ]
}
```

There is still a key for every region, but the list of the closest regions is
limited to those specified on the command line.

## From Whence Comes This Data?
This Gist: https://gist.github.com/atyachin/a011edf76df66c5aa1eac0cdca412ea9

Instead of geographic coordinates, it'd probably be better if we sorted by 
actual inter-region latency, like what this table has: 
https://www.cloudping.co/grid

Put it on the TODO list.
