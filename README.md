Backend Router terraform module
===============================

This module creates a Backend Router (BR) service which, in effect, is a shared ALB to which individual services can be attached.
Ideally, there should be a single BR per Team (eg. platform-backend-router).

The BR consists of:
- **internal** ALB - this ALB should never be a public ALB
- default, HTTPS Listener, with certificate as per `dns_domain` parameter, by default diverting traffic to `404` ECS Service
- 404 ECS Service which deployes all the required components (404 ECS Service, 404 Target Group, 404 IAM Role and Policy and) and runs a tiny binary which returns 404 to every call

Services attached to this ALB should be using `host-based` conditions for routing, rather than `path-based`.

Module Input Variables
----------------------

- `team` - (string) - **REQUIRED** - Name of Team deploying the ALB - will affect ALBs name
- `env` - (string) - **REQUIRED** - Environment deployed to
- `component` - (string) - **REQUIRED** - component name
- `platform_config` - (map) - **REQUIRED** - Mergermarket Platform config dictionary (see tests for example one)
- `dns_domain` - (string) - domain to be used when looking up SSL Certificate

Usage
-----
```hcl
module "backend_router" {
  source = "../.."

  team            = "footeam"
  env             = "fooenv"
  component       = "foocomponent"
  platform_config = "{\"foo\":\"bar\"}"

  # optional
  dns_domain = "domain.com"
}
```

Outputs
-------
- `alb_dns_name` - The DNS name of the load balancer
- `alb_listener_arn` - The ARN of the load balancer
