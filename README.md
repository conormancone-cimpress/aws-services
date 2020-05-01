```
{
    "EC2": {
        "instances": {
            "options": [
                {"type": "On Demand", "description": "Standard compute solution.  Pricing depends on Instance Type"},
                {"type": "Spot", "description": "Squeezes work into excess compute power.  Pricing fluctuates based on demand, typically a fraction (70%-90% off) of On Demand pricing.  Low priority service: it will experience interruptions"},
                {"type": "Savings Plans", "Effectively, purchase a total number of compute hours before hand for a large discount.  Discounts around ~75% when you purchase 1-3 years of compute time in advance"},
                {"type": "Reserved Instances", "description": "Reserve a machine for 1-3 years at a large discount (~75%)"},
                {"type": "Dedicated Hardware", "description": "You can actually get your own server!"},
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
        "network interfaces": {
            "see": "https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/network_mtu.html"
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
