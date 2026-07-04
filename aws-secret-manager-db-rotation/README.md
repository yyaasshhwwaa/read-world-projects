This document summarizes the key security and compliance considerations for managing database credentials on AWS, especially for regulated environments such as loan servicing and payment platforms.



- PCI-DSS Requirement 8.3 requires regular rotation of passwords and credentials, typically every 30–90 days.
- Credentials must never be hardcoded or stored in plaintext.
- ISO 27001 Annex A.9 emphasizes that access credentials should be controlled, audited, and rotated as part of security policy.
- For organizations handling payments, loans, or sensitive customer data, this is not optional.


A common pattern is storing credentials like this in a .env file or config:

```env
DB_PASSWORD=mypassword123
```

This approach is risky because a leaked repository, compromised EC2 instance, or insider misuse can expose secrets quickly.



- Simple to implement.
- Uses one secret.
- Has a guaranteed downtime window during each rotation cycle.
- The application may fail temporarily when the database password changes and old credentials are still in use.
- Suitable for development or test environments.
- Riskier for production systems.


- Better for production environments.
- Uses two database users, such as appuser_a and appuser_b.
- Rotation updates the standby user, then switches over.
- The active user is never touched.
- Helps avoid downtime and connection failures.
- This is the recommended pattern for regulated systems.


Password rotation addresses current risk, but modern AWS-native architecture is moving beyond passwords:

- AWS IAM Database Authentication uses short-lived tokens.
- Tokens are valid for about 15 minutes.
- No password is stored in application configuration.
- Rotation is no longer required in the same way.


1. Hardcoded passwords
2. Secrets Manager rotation
3. IAM authentication with no stored passwords


For AWS RDS with PostgreSQL or MySQL and regulated data, the alternating user rotation pattern is the minimum bar. IAM authentication is the long-term target architecture.


