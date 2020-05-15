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
        "launch templates": {
        },
        "spot requests": {
        },
        "savings plans": {
        },
        "reserved instances": {
        },
        "dedicated hosts": {
        },
        "scheduled instances": {
        },
        "capacity reservations": {
        },
        "AMIs": {
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
        "elastic ips": {
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
        "launch configurations": {
        },
        "auto scaling groups": {
        },
    },
}
```
