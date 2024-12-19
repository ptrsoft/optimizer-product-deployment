https://learnk8s.io/production-best-practices

# Health checks

1. Containers have Readiness probes
2. Containers crash when there's a fatal error
3. Configure a passive Liveness probe
4. Liveness probes values aren't the same as the Readiness

# Apps are independent

1. The Readiness probes are independent
2. The app retries connecting to dependent services

# Graceful shutdown

1. The app doesn't shut down on SIGTERM, but it gracefully terminates connections
2. The app still processes incoming requests in the grace period
3. The CMD in the Dockerfile forwards the SIGTERM to the process
4. Close all idle keep-alive sockets

# Fault tolerance

1.  Run more than one replica for your Deployment
2.  Avoid Pods being placed into a single node
3.  Set Pod disruption budgets

# Resources utilisation

1.  Set memory limits and requests for all containers
2.  Set CPU request to 1 CPU or below
3.  Disable CPU limits — unless you have a good use case
4.  The namespace has a LimitRange
5.  Set an appropriate Quality of Service (QoS) for Pods
   
# Tagging resources

1. Resources have technical labels defined
2. Resources have business labels defined
3. Resources have security labels defined

# Logging

1. The application logs to stdout and stderr
2. Avoid sidecars for logging (if you can)

# Scaling

1. Containers do not store any state in their local filesystem
2. Use the Horizontal Pod Autoscaler for apps with variable usage patterns
3. Don't use the Vertical Pod Autoscaler while it's still in bet
4. Use the Cluster Autoscaler if you have highly varying workloads

# Configuration and secrets

1. Externalise all configuration
2. Mount Secrets as volumes, not enviroment variables

# Governance

1. Namespaces have LimitRange
2. Namespaces have ResourceQuotas

# Pod security policies

1. Enable Pod Security Policies
2. Disable privileged containers
3. Use a read-only filesystem in containers
4. Prevent containers from running as root
5. Limit capabilities
6. Prevent privilege escalation

# Network policies

1. Enable network policies
2. There's a conservative NetworkPolicy in every namespace

# Role-Based Access Control (RBAC) policies

1. Disable auto-mounting of the default ServiceAccount
2. RBAC policies are set to the least amount of privileges necessary
3. RBAC policies are granular and not shared

# Custom policies

1. Allow deploying containers only from known registries
2. Enforce uniqueness in Ingress hostnames
3. Only use approved domain names in the Ingress hostnames

# Cluster configuration

1. The cluster passes the CIS benchmark
2. Disable metadata cloud providers metadata API
3. Restrict access to alpha or beta features

# Authentication

1. Use OpenID (OIDC) tokens as a user authentication strategy

# Role-Based Access Control (RBAC)

1. ServiceAccount tokens are for applications and controllers only

# Logging setup

1.  There's a retention and archival strategy for logs
2.  Logs are collected from Nodes, Control Plane, Auditing
3.  Prefer a daemon on each node to collect the logs instead of sidecars
4.  Provision a log aggregation tool