# Schools configuration

This repository contains non-secret Spring Boot 4 configuration. Service application.yml files identify each application; Config Server supplies the prod-container configuration. Each of gateway, information, finance, and security has one active file: <service>-prod-container.yml, including its port, application name, and any servlet context path. Clients must request the prod-container config profile; there are no base <service>.yml files. Docker already selects prod-container. Config Server and Eureka retain bootstrap settings locally to avoid circular startup dependencies.

Production container service names: configserver:8076, eureka:8071, gateway:8070, information:8072, finance:8074, security:8075, zipkin:9411. MySQL and Kafka keep their existing host.docker.internal destinations.

Required runtime variables are resolved in the client container: DB_USERNAME, DB_PASSWORD (database services) and JWT_SECRET (legacy finance/information settings only). Do not define their values in Config Server's environment as a substitute for client secrets. No fallback production passwords are provided.

Security now signs with its persistent RSA private key, and gateway validates via the internal JWKS URL. They no longer share JWT_SECRET. Config Server's own CONFIG_ENCRYPTION_KEY is supplied to its container by its service repository workflow. Git credentials are only required if this configuration repository is made private.

Docker profiles are complete and centralize logging, tracing, datasource, service URLs, and Feign timeouts. Archived local, test, and Kubernetes profiles are kept under unused/ and are not loaded from the repository root.

The history may still contain previously exposed credentials. Removing values from current files does not revoke those credentials. Rotate database credentials before treating them as private.

No runtime secrets belong in this repository. Development/test credentials should be supplied in local environment variables. OAuth registrations require actual provider credentials; template credentials are not deployed.

## Production deployment host

The single source of truth is `DEPLOY_HOST` in [deployment.json](deployment.json). All 18 Docker, JAR, and Kubernetes deployment workflows load this file from the main branch before deployment. Change it once to affect subsequent workflow runs. Organization and repository variables named DEPLOY_HOST are not used. Application traffic uses Docker service DNS names. A new host still needs provisioning and trusted SSH host keys; changing this file does not migrate existing workloads.
