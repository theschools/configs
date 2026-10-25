# Schools configuration

This repository contains non-secret Spring Boot 4 configuration. Service application.yml files identify each application; Config Server supplies service defaults and the active profile. Docker uses prod-container. Config Server and Eureka retain bootstrap settings locally to avoid circular startup dependencies.

Production container service names: configserver:8076, eureka:8071, gateway:8070, information:8072, finance:8074, security:8075, zipkin:9411. MySQL and Kafka keep their existing host.docker.internal destinations.

Required runtime variables are resolved in the client container: DB_USERNAME, DB_PASSWORD (database services) and JWT_SECRET (all four application services). Do not define their values in Config Server's environment as a substitute for client secrets. No fallback production passwords are provided.

The same JWT signing value must be configured in the four corresponding GitHub repositories. Config Server's own CONFIG_ENCRYPTION_KEY is supplied to its container by its service repository workflow. Git credentials are only required if this configuration repository is made private.

Docker profiles are complete and centralize logging, tracing, datasource, service URLs, and Feign timeouts. Local and test profiles retain their environment-specific addresses. Kubernetes profiles retain their existing topology; credential literals were replaced with environment references as part of removing secrets from this repository.

The history may still contain previously exposed credentials. Removing values from current files does not revoke those credentials. Rotate database credentials before treating them as private.

No runtime secrets belong in this repository. Development/test credentials should be supplied in local environment variables. OAuth registrations require actual provider credentials; template credentials are not deployed.

## Production deployment host

The single source of truth is the theschools GitHub organization Actions variable `DEPLOY_HOST`. All six service repositories have access to it, and their deployment workflows read `${{ vars.DEPLOY_HOST }}` with no IP fallbacks. Change the organization variable once to affect subsequent workflow runs. Do not define repository or environment overrides. Application traffic uses Docker service DNS names, so it does not need copies of the production IP in this repository. Changing the host also requires preparing the new server and updating trusted SSH host keys; it does not migrate existing workloads.
