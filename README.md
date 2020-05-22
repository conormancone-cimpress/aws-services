```
{
    "EC2": {
        "instances": {
            "options": [
                {"type": "On Demand", "description": "Standard compute solution.  Pricing depends on Instance Type"},
                {"type": "Spot", "description": "Squeezes work into excess compute power.  Pricing fluctuates based on demand, typically a fraction (70%-90% off) of On Demand pricing.  Low priority service: it will experience interruptions"},
                {"type": "Savings Plans", "Effectively, purchase a total number of compute hours before hand for a large discount.  Discounts around ~75% when you purchase 1-3 years of compute time in advance"},
                {"type": "Reserved Instances", "description": "Reserve a machine for 1-3 years at a large discount (~75%)"},
                {"type": "Dedicated Instances", "description": "Only your stuff will go on the physical machine!"},
                {"type": "Dedicated Hosts", "description": "Like dedicated instances but tied to a particular piece of hardware.  Typical use case is for software licenses that are tied to a single physical machine"},
                {"type": "Scheduled Instances", "description": "Weird. Starts and stops on a schedule.  No flexibility.  Cheaper?"}
            ],
            "data_transfer": [
                {"from": "Internet", "to": "AWS", "price": "free"},
                {"from": "AWS", "to": "Internet", "price": "first GB free, then tiered, Next 10 TB, Next 40 TB, Next 100TB, >150TB/Month. $0.09/GB - $0.05/GB"}
                {"from": "AZ", "to": "Same AZ": "price": "free"},
                {"from": "AWS", "to": "Other Places", "price": "Complicated"},
            ],
        },
        "instance types": [
            {"type": "General Purpose", "description": "Exactly what it sounds like"},
            {"type": "Compute Optimized", "description": "Exactly what it sounds like"},
            {"type": "Memory Optimized", "description": "What did you think you were going to find here?"},
            {"type": "Storage Optimized", "description": "Seriously, give it up"},
            {"type": "Burstable Performance", "description": "Earn credits while under utilized, and then can spend those credits when usage increases.  However there is a cap on accrued credits, and a limit to maximum CPU performance when bursting.  For T2 the credits are lost if the instance is stopped, but for T3 and T3a they hang out for a week.  You then choose between 'unlimited' and 'standard'.  Unlimited can burst high for as long as needed at extra cost.  As long as average CPU utilization stays under 100% over 24 hours, no additional costs will be accrued (in essence the maximum credits are set so that they top out after 24 hours, averaging out any spikes in a 24 hour period).  For 'standard' if you spike too high for too long, performance will be reduced slowly so that when credits run out you are limited to 100% usage"},
            {"type": "Accelerated", "description": "Includes support for GPUs and FGPAs"}

        ],
        "spot requests": {
        },
        "savings plans": {
        },
        "capacity reservations": {
        },
        "AMIs": {
            "permissions": [
                {
                    "name": "Public",
                    "description": "Available to anyone anywhere"
                },
                {
                    "name": "Explicit",
                    "description": "Specify allowed accounts",
                },
                {
                    "name": "Implicit",
                    "description": "In essence, me only",
                }
            ],
            "storage": [
                {
                    "name": "EBS",
                    "description": "Backed by EBS",
                    "notes": "EBS is network backed, so lower performance but can persist longer than instance and can move from instance to instance"
                },
                {
                    "name": "Instance Store",
                    "description": Backed by an instance store volume",
                    "notes": "instance-store is physically attached to the instance and cannot move or persist, but gets better performance.  Remember that instance-store backed instances cannot be stopped: they are either running or terminated"
                }
            ],
            "virtualization_types": [
                {
                    "name": "Hardware Virtual Machine (HVM)",
                    "description": "Supported by all instance types.  HVM provides 'closer' access to the underlying hardware, making it possible for the OS to take advantage of hardware-specific features.  This is required for use of GPUs and enhanced networking."
                },
                {
                    "name": "Paravirtual",
                    "description": "Can run on hosts that don't explicitly support virtualization, but doesn't have support for GPUs or enhanced networking.  PV used to have better performance than HVM, but this isn't true anymore."
                }
            ],

        },
        "bundle tasks": {
        },
        "ebs volumes": {
        },
        "ebs snapshots": {
        },
        "ebs lifecycle manager": {
        },
        "security groups": {
        },
        "placement groups": {
        },
        "key pairs": {
        },
        "networking": {
            "IP Addresses": [
                {
                    "type": "multiple",
                    "description": "You can have multiple IP addresses per network interface, limited by instance type",
                    "usage": [
                        "IP addresses can be moved between instances to allow for quick failover for broken instances",
                        "Multiple IP addresses allows a server to host multiple SSL certs if the certs are one-per-IP",
                        "Useful for load balancers/firewalls"
                    ],
                },
                {
                    "type": "BYOIP",
                    "description": "If you own an IP address space you can have AWS advertise it for you so you can use it in AWS",
                },
                {
                    "type": "Elastic",
                    "description": "A 'standard" public IP address that can be moved from account to account",
                    "notes": [
                        "Can be moved around as needed",
                        "One IP per running instance is free",
                    ]
                }
            ],
            "Enhanced Networking": {
                "Description": "An Elastic Network Adapter gives 'enhanced' networking but requires but OS support is required.  Benefits are significantly higher packet per second (PPS) performance, lower network jitter and lower latencies.  Not supported on all instance types.  Instance types that support it have higher maximum bandwidth (usually 25Gbps instead of 10Gbps) and ENA is required to acheive this.",
            },
            "Elastic Fabric Adapter": {
                "Description": "Designed for HPC and other cluster-centric processes.  Grants the very high inter-instance throughput needed to support these kinds of processes (aka cluster 100s/1000s of GPUs/CPUs)"
            },
            "MTU": {
                "aka": "frame size",
                "description": "The MTU (maximum transmission unit) is the maximum allowed size in a IP packet.  Since each packet contains overhead, larger MTU == more efficient communication (and probably slightly higher bandwidth)",
                "types": [
                    {
                        "name": "1500",
                        "aka": "The standard",
                        "description": "standard ethernet v2 frame format supports 1500 bytes per packet.  This is pretty much the standard on the internet.  All EC2 instances support this"
                    },
                    {
                        "name": "9001",
                        "aka": "Jumbo Frames",
                        "description": "Some instance types support 'Jumbo Frames' with up to 9001 bytes per packet for higher network speeds.  However by default these will be broken up (or dropped if `Don't Fragment` is set on the IP header) when leaving a VPC.  If that happens you'll actually send up with worse bandwidth, so this must be used with care. It's possible to configure a route that supports jumbo frames and set jumbo frame support per-network interface.  Finally when jumbo frames are needed the best practice is to create a placement group that supports it and co-locate the instances in it.  Internet gateways will only ever transmit 1500MTU frames, so don't bother trying to use jumbo frames across the internet."
                    }
                ],
                "path_discovery": "You can figure out the MTU on a route by sending large packets with the `Don't Fragment` flag set.  When rejected an ICMP message with a rejection message will be sent to the sending machine.  Normally security groups block all ICMP messages, but if it is in response to an outgoing message the notice will be allowed through.  `tracepath` can be used to do this."
            },
            "Placement Group": {
                "cost": "free",
                "description": "Used to determine how your instances are actually distributed across physical hardware",
                "types": [
                    {
                        "name": "cluster",
                        "description": "Keeps nodes physically close to minimize latency"
                    },
                    {
                        "name": "partition",
                        "description": "Compromise solution.  Group is divided into partitions and each partition is assigned to its own server rack, minimizing the odds of losing lots of nodes at once"
                    },
                    {
                        "name": "spread",
                        "description": "Each instance is placed on its own rack.  Maximum durability for cases where multiple lost instances at the same time is a critical issue.  Max of 7 instances per availability zone per group.  If you request such a group and sufficient hardware is not available your request will be denied"
                    }
                ],
                "restrictions": [
                    {
                        "type": "general",
                        "limits": [
                            "No launching instances with 'host' tenancy in placement groups"
                        ]
                    },
                    {
                        "type": "cluster",
                        "limits": [
                            "Not all instance types are available",
                            "Cannot span AZs",
                            "It's safest to use only one instance type, because that makes it easier to actually place the nodes",
                            "Lower limit on outgoing bandwidth (5Gbps instead of 10Gbps)
                        ]
                    },
                    {
                        "type": "partition",
                        "limits": [
                            "maximum 7 partitions per AZ",
                            "We try our best for even distribution, but no promises",
                            "If you add in dedicated instances then you're only allowed 2 partitions per AZ"
                        ]
                    },
                    {
                        "type": "spread",
                        "limits": [
                            "Max 7 instances per AZ",
                            "No dedicated instances"
                        ]
                    }
                ]
            }
        },
        "load balancers": {
        },
        "target groups": {
        },
        "auto scaling groups": {
            "lifecycle": [
                {
                    "name": "Hook: EC2 Instance Launching",
                    "description": "If you attach an 'instance launching' hook then when an instance becomes 'Pending' your hook will execute and the instance will enter the 'Pending: wait' step until your hook finishes.  Then it will transition to 'Pending: proceed' and from there to 'In Service"
                },
                {
                    "name": "Detaching",
                    "description": "You can separate an instance from the ASG and 'spint it off' as an independent instance"
                },
                {
                    "name": "Attaching",
                    "description": "You can take an independent instance and attach it to the ASG so that it is managed as part of the ASG"
                },
                {
                    "name": "Standby",
                    "description": "Temporarily remove instances from service for debugging/etc purposes.  https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-enter-exit-standby.html"
                }
            ],
            "configuration": {
                "description": "Launch configuration or Launch template is required.  You can also specify an EC2 instance, but then AWS just converts that into a launch configuration to attach",
                "types": [
                    {
                        "name": "Launch Configuration",
                        "description": "Not editable, and only one launch configuration can be attached to an ASG at a time.  Has some limits, for instance cannot Spot and On Demand instances into the same ASG with a launch configuration."
                    },
                    {
                        "name": "Launch Template",
                        "description": "More flexible and generally recommended.  They can extend eachother and have multiple versions, allowing for more flexibility.  Also allows you to launch different instance types in the same ASG.  You can convert a Launch Configuration into a Launch template and update an ASG that uses a launch configuration to use a Launch template instead."
                    }
                ]
            }
        },
        "monitoring": [
            {
                "type": "Automated",
                "options": [
                    {
                        "name": "System status checks",
                        "description": "Checks on the underlying AWS infrastructure - with the exception of restarting or moving instances, only AWS can fix issues with these"
                    },
                    {
                        "name": "Instance status checks",
                        "description": "checks on (in essence) the os: memory usage, file system, network configuration, etc... if these fails then you screwed up your instance and need to fix it"
                    },
                    {
                        "name": "CloudWatch alarms",
                        "description": "Choose a metric to monitor with cloudwatch and trigger an action (notice to Amazon SNS topic or trigger EC2 Autoscaling policy).  Note that action is only taken as a result of a sustained change in the metric"
                    },
                    {
                        "name": "CloudWatch Events",
                        "description": "https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html"
                    },
                    {
                        "name": "Cloud Watch Logs",
                        "description": "https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/"
                    },
                    {
                        "name": "Cloud Watch Agent",
                        "description": "https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html"
                    }
                ]
            },
            {
                "type": "Plan",
                "description": "A good monitoring plan is critical.  It will let you address problems while still small.",
                "questions" [
                    "What are your goals for monitoring?",
                    "What resources you will monitor?",
                    "How often you will monitor these resources?",
                    "What monitoring tools will you use?",
                    "Who will perform the monitoring tasks?",
                    "Who should be notified when something goes wrong?"
                ]
            },
            {
                "type": "Cloudwatch",
                "description": "CloudWatch collects and processes raw data from EC2 to present readable and near-real-time metrics.  Data is recorded for 15 months.  Sends data to Cloudwatch every 15 minutes, and presents graphs from CloudWatch in EC2 console",
                "options": [
                    {
                        "name": "Detailed Monitoring",
                        "description": "Data is available in 1 minute periods at an additional charge"
                    },
                    {
                        "name": "Aggregate across instances",
                        "description": "For detailed monitoring only, you can show average/total metrics for a number of instances"
                    },
                    {
                        "name": "Aggregate across autoscaling groups",
                        "description": "You can show average/total metrics for instances in an ASG.  Not sure if detailed metrics are required"
                    },
                    {
                        "name": "Aggregate across AMI",
                        "description": "For detailed monitoring only, you can show average/total metrics for instances using a given AMI"
                    },
                    {
                        "name": "Create Alarm",
                        "description": "You can set an alarm to trigger depending on metrics.  The alarm can be sent to email addresses as well as an SNS topic.  It can also stop/start/restart instances"
                    }
                ]

            }
        ]
    },
}
```
