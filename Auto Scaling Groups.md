### Auto Scaling Group (ASG)

A **managed group of identical servers** that:

- Automatically **adds instances** when load increases
    
- Automatically **removes instances** when load decreases
    
- Replaces **dead/unhealthy instances**
    

Example:

```yaml
API Service ASG:
- min: 2 servers
- desired: 5 servers
- max: 20 servers
```

If CPU > 70% → add servers  
If instance crashes → replace automatically

Why it matters:

- Fault tolerance
    
- Elastic scaling
    
- No “pet servers” (everything is replaceable)