# AWS WAF

The AWS WAF integration allows you to monitor [AWS Web Application Firewall (WAF)](https://aws.amazon.com/waf/)—a web application firewall for protecting against common web exploits.

Use the AWS WAF integration to collect and parse logs related to firewall activity across your AWS infrastructure.
Then visualize that data in Kibana, create alerts to notify you if something goes wrong,
and reference logs when troubleshooting an issue.

For example, you could use the data from this integration to spot unusual SQL injection and cross-site scripting attempts on your AWS-hosted websites and web applications, in a given time period. You could also use the data to review or troubleshoot the rules that have been set up to block these web exploits. You can do this by looking at additional context in the logs, such as the source of the requests, and more.

**IMPORTANT: Extra AWS charges on AWS API requests will be generated by this integration. Please refer to the AWS integration for more details.**

## Data streams

The AWS WAF integration collects one type of data: logs.

**Logs** help you keep a record of events happening in AWS WAF.
Logs collected by the AWS WAF integration include information on the rule that terminated a request, the source of the request, and more. See more details in the [Logs reference](#logs-reference).

> Note: The `waf` data stream is specifically for WAF logs.

## Requirements

You need Elasticsearch for storing and searching your data and Kibana for visualizing and managing it.
You can use our hosted Elasticsearch Service on Elastic Cloud, which is recommended, or self-manage the Elastic Stack on your own hardware.

Before using any AWS integration you will need:

* **AWS Credentials** to connect with your AWS account.
* **AWS Permissions** to make sure the user you're using to connect has permission to share the relevant data.

For more details about these requirements, see the **AWS** integration documentation.

## Setup

Use this integration if you only need to collect data from the AWS WAF service.

If you want to collect data from two or more AWS services, consider using the **AWS** integration.
When you configure the AWS integration, you can collect data from as many AWS services as you'd like.

For step-by-step instructions on how to set up an integration, see the
[Getting started](https://www.elastic.co/guide/en/welcome-to-elastic/current/getting-started-observability.html) guide.

### Advanced options

#### CloudWatch

The CloudWatch logs input has several advanced options to fit specific use cases.

##### Latency

AWS CloudWatch Logs sometimes takes extra time to make the latest logs available to clients like the Agent.

The CloudWatch integration offers the `latency` setting to address this scenario. Latency translates the query's time range to consider the CloudWatch Logs latency. For example, a `5m` latency means the integration will query CloudWatch for logs available 5 minutes ago.

##### Number of workers

If you are collecting log events from multiple log groups using `log_group_name_prefix`, you should review the value of the `number_of_workers`.

The `number_of_workers` setting defines the number of workers assigned to reading from log groups. Each log group matching the `log_group_name_prefix` requires a worker to keep log ingestion as close to real-time as possible. For example, if `log_group_name_prefix` matches five log groups, then `number_of_workers` should be set to `5`. The default value is `1`.

## Logs reference

The `waf` dataset is specifically for WAF logs. Export logs from Kinesis Data Firehose to Amazon S3 bucket which has SQS notification setup already.

**Exported fields**

| Field | Description | Type |
|---|---|---|
| @timestamp | Event timestamp. | date |
| aws.waf.arn | AWS ARN of ACL | keyword |
| aws.waf.id | ID of ACL | keyword |
| aws.waf.non_terminating_matching_rules | The list of non-terminating rules in the rule group that match the request. These are always COUNT rules (non-terminating rules that match) | nested |
| aws.waf.rate_based_rule_list | The list of rate-based rules that acted on the request. | nested |
| aws.waf.request.headers | List of request headers | flattened |
| aws.waf.rule_group_list | The list of rule groups that acted on this request. | nested |
| aws.waf.source.id | The source ID. This field shows the ID of the associated resource. | keyword |
| aws.waf.source.name | The source of the request. Possible values: CF for Amazon CloudFront, APIGW for Amazon API Gateway, ALB for Application Load Balancer, and APPSYNC for AWS AppSync. | keyword |
| aws.waf.terminating_rule_match_details | Detailed information about the terminating rule that matched the request. A terminating rule has an action that ends the inspection process against a web request. Possible actions for a terminating rule are ALLOW and BLOCK. This is only populated for SQL injection and cross-site scripting (XSS) match rule statements. As with all rule statements that inspect for more than one thing, AWS WAF applies the action on the first match and stops inspecting the web request. A web request with a terminating action could contain other threats, in addition to the one reported in the log. | nested |
| cloud.account.id | The cloud account or organization id used to identify different entities in a multi-tenant environment. Examples: AWS account id, Google Cloud ORG Id, or other unique identifier. | keyword |
| cloud.account.name | The cloud account name or alias used to identify different entities in a multi-tenant environment. Examples: AWS account name, Google Cloud ORG display name. | keyword |
| cloud.availability_zone | Availability zone in which this host, resource, or service is located. | keyword |
| cloud.image.id | Image ID for the cloud instance. | keyword |
| cloud.instance.id | Instance ID of the host machine. | keyword |
| cloud.instance.name | Instance name of the host machine. | keyword |
| cloud.machine.type | Machine type of the host machine. | keyword |
| cloud.project.id | The cloud project identifier. Examples: Google Cloud Project id, Azure Project id. | keyword |
| cloud.provider | Name of the cloud provider. Example values are aws, azure, gcp, or digitalocean. | keyword |
| cloud.region | Region in which this host, resource, or service is located. | keyword |
| container.id | Unique container id. | keyword |
| container.image.name | Name of the image the container was built on. | keyword |
| container.labels | Image labels. | object |
| container.name | Container name. | keyword |
| data_stream.dataset | Data stream dataset. | constant_keyword |
| data_stream.namespace | Data stream namespace. | constant_keyword |
| data_stream.type | Data stream type. | constant_keyword |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |
| error.message | Error message. | match_only_text |
| event.action | The action captured by the event. This describes the information in the event. It is more specific than `event.category`. Examples are `group-add`, `process-started`, `file-created`. The value is normally defined by the implementer. | keyword |
| event.dataset | Event dataset | constant_keyword |
| event.id | Unique ID to describe the event. | keyword |
| event.kind | This is one of four ECS Categorization Fields, and indicates the highest level in the ECS category hierarchy. `event.kind` gives high-level information about what type of information the event contains, without being specific to the contents of the event. For example, values of this field distinguish alert events from metric events. The value of this field can be used to inform how these kinds of events should be handled. They may warrant different retention, different access control, it may also help understand whether the data coming in at a regular interval or not. | keyword |
| event.module | Event module | constant_keyword |
| event.outcome | This is one of four ECS Categorization Fields, and indicates the lowest level in the ECS category hierarchy. `event.outcome` simply denotes whether the event represents a success or a failure from the perspective of the entity that produced the event. Note that when a single transaction is described in multiple events, each event may populate different values of `event.outcome`, according to their perspective. Also note that in the case of a compound event (a single event that contains multiple logical events), this field should be populated with the value that best captures the overall success or failure from the perspective of the event producer. Further note that not all events will have an associated outcome. For example, this field is generally not populated for metric events, events with `event.type:info`, or any events for which an outcome does not make logical sense. | keyword |
| host.architecture | Operating system architecture. | keyword |
| host.containerized | If the host is a container. | boolean |
| host.domain | Name of the domain of which the host is a member. For example, on Windows this could be the host's Active Directory domain or NetBIOS domain name. For Linux this could be the domain of the host's LDAP provider. | keyword |
| host.hostname | Hostname of the host. It normally contains what the `hostname` command returns on the host machine. | keyword |
| host.id | Unique host id. As hostname is not always unique, use values that are meaningful in your environment. Example: The current usage of `beat.name`. | keyword |
| host.ip | Host ip addresses. | ip |
| host.mac | Host MAC addresses. The notation format from RFC 7042 is suggested: Each octet (that is, 8-bit byte) is represented by two [uppercase] hexadecimal digits giving the value of the octet as an unsigned integer. Successive octets are separated by a hyphen. | keyword |
| host.name | Name of the host. It can contain what `hostname` returns on Unix systems, the fully qualified domain name, or a name specified by the user. The sender decides which value to use. | keyword |
| host.os.build | OS build information. | keyword |
| host.os.codename | OS codename, if any. | keyword |
| host.os.family | OS family (such as redhat, debian, freebsd, windows). | keyword |
| host.os.kernel | Operating system kernel version as a raw string. | keyword |
| host.os.name | Operating system name, without the version. | keyword |
| host.os.name.text | Multi-field of `host.os.name`. | match_only_text |
| host.os.platform | Operating system platform (such centos, ubuntu, windows). | keyword |
| host.os.version | Operating system version as a raw string. | keyword |
| host.type | Type of host. For Cloud providers this can be the machine type like `t2.medium`. If vm, this could be the container, for example, or other information meaningful in your environment. | keyword |
| http.request.id | A unique identifier for each HTTP request to correlate logs between clients and servers in transactions. The id may be contained in a non-standard HTTP header, such as `X-Request-ID` or `X-Correlation-ID`. | keyword |
| http.request.method | HTTP request method. The value should retain its casing from the original event. For example, `GET`, `get`, and `GeT` are all considered valid values for this field. | keyword |
| http.version | HTTP version. | keyword |
| network.protocol | In the OSI Model this would be the Application Layer protocol. For example, `http`, `dns`, or `ssh`. The field value must be normalized to lowercase for querying. | keyword |
| network.transport | Same as network.iana_number, but instead using the Keyword name of the transport layer (udp, tcp, ipv6-icmp, etc.) The field value must be normalized to lowercase for querying. | keyword |
| related.ip | All of the IPs seen on your event. | ip |
| rule.id | A rule ID that is unique within the scope of an agent, observer, or other entity using the rule for detection of this event. | keyword |
| rule.ruleset | Name of the ruleset, policy, group, or parent category in which the rule used to generate this event is a member. | keyword |
| source.address | Some event source addresses are defined ambiguously. The event will sometimes list an IP, a domain or a unix socket.  You should always store the raw address in the `.address` field. Then it should be duplicated to `.ip` or `.domain`, depending on which one it is. | keyword |
| source.as.number | Unique number allocated to the autonomous system. The autonomous system number (ASN) uniquely identifies each network on the Internet. | long |
| source.as.organization.name | Organization name. | keyword |
| source.as.organization.name.text | Multi-field of `source.as.organization.name`. | match_only_text |
| source.geo.city_name | City name. | keyword |
| source.geo.continent_name | Name of the continent. | keyword |
| source.geo.country_iso_code | Country ISO code. | keyword |
| source.geo.country_name | Country name. | keyword |
| source.geo.location | Longitude and latitude. | geo_point |
| source.geo.region_iso_code | Region ISO code. | keyword |
| source.geo.region_name | Region name. | keyword |
| source.ip | IP address of the source (IPv4 or IPv6). | ip |
| tags | List of keywords used to tag each event. | keyword |
| url.path | Path of the request, such as "/search". | wildcard |
| url.query | The query field describes the query string of the request, such as "q=elasticsearch". The `?` is excluded from the query string. If a URL contains no `?`, there is no query field. If there is a `?` but no query, the query field exists with an empty string. The `exists` query can be used to differentiate between the two cases. | keyword |


An example event for `waf` looks as following:

```json
{
    "@timestamp": "2021-11-25T14:25:25.000Z",
    "data_stream": {
        "namespace": "default",
        "type": "logs",
        "dataset": "aws.waf"
    },
    "rule": {
        "ruleset": "REGULAR",
        "id": "STMTest_SQLi_XSS"
    },
    "source": {
        "geo": {
            "continent_name": "Oceania",
            "country_name": "Australia",
            "location": {
                "lon": 143.2104,
                "lat": -33.494
            },
            "country_iso_code": "AU"
        },
        "as": {
            "number": 13335,
            "organization": {
                "name": "Cloudflare, Inc."
            }
        },
        "ip": "1.1.1.1"
    },
    "tags": [
        "preserve_original_event"
    ],
    "network": {
        "protocol": "http",
        "transport": "tcp"
    },
    "cloud": {
        "region": "ap-southeast-2",
        "provider": "aws",
        "service": {
            "name": "wafv2"
        },
        "account": {
            "id": "12345"
        }
    },
    "ecs": {
        "version": "8.0.0"
    },
    "related": {
        "ip": [
            "1.1.1.1"
        ]
    },
    "http": {
        "request": {
            "method": "POST",
            "id": "null"
        },
        "version": "1.1"
    },
    "event": {
        "action": "BLOCK",
        "ingested": "2021-10-11T15:00:35.544818361Z",
        "original": "{\"timestamp\":1576280412771,\"formatVersion\":1,\"webaclId\":\"arn:aws:wafv2:ap-southeast-2:12345:regional/webacl/test/111\",\"terminatingRuleId\":\"STMTest_SQLi_XSS\",\"terminatingRuleType\":\"REGULAR\",\"action\":\"BLOCK\",\"terminatingRuleMatchDetails\":[{\"conditionType\":\"SQL_INJECTION\",\"location\":\"UNKNOWN\",\"matchedData\":[\"10\",\"AND\",\"1\"]}],\"httpSourceName\":\"ALB\",\"httpSourceId\":\"alb\",\"ruleGroupList\":[],\"rateBasedRuleList\":[],\"nonTerminatingMatchingRules\":[],\"requestHeadersInserted\":null,\"responseCodeSent\":null,\"httpRequest\":{\"clientIp\":\"1.1.1.1\",\"country\":\"AU\",\"headers\":[],\"uri\":\"\",\"args\":\"\",\"httpVersion\":\"HTTP/1.1\",\"httpMethod\":\"POST\",\"requestId\":\"null\"},\"labels\":[{\"name\":\"value\"}]}",
        "category": "web",
        "type": [
            "access",
            "denied"
        ],
        "kind": "event"
    },
    "aws": {
        "waf": {
            "terminating_rule_match_details": [
                {
                    "conditionType": "SQL_INJECTION",
                    "location": "UNKNOWN",
                    "matchedData": [
                        "10",
                        "AND",
                        "1"
                    ]
                }
            ],
            "id": "regional/webacl/test/111",
            "source": {
                "name": "ALB",
                "id": "alb"
            },
            "arn": "arn:aws:wafv2:ap-southeast-2:12345:regional/webacl/test/111"
        }
    }
}
```