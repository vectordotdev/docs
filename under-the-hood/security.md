---
description: Timber's security information
---

# Security

The security and privacy of your data is critically important to Timber and has been a primary concern since Timber was created. The Timber team comes from strong enterprise software experience, working with health and financial institutions to meet stringent security and compliance standards. This knowledge, and the practices that accompany them, have been implemented into Timber since day one.

## Data in-transit

### Protocols

All communication with the Timber service is secured using [Transport Layer Security \(TLS\)](https://en.wikipedia.org/wiki/Transport_Layer_Security) and the latest SHA256 certification for encryption. The Timber service will not accept connections on un-encrypted protocols. This is true for both incoming and outgoing data.

### SSL Certificates

Timber's SSL certificates are purchased directly with [DigiCert](https://www.digicert.com/), who has demonstrated best-in-class security.

### API keys

Each endpoint is authorized with an API key that is protected using a [SHA256 keyed-hash message authentication code \(HMAC\)](https://en.wikipedia.org/wiki/HMAC).

### Encrypted VPCs

Once data is accepted by Timber is never exposed to the public internet. All communication between internal Timber systems is done within a [VPC](https://en.wikipedia.org/wiki/Virtual_private_cloud), and all communication is encrypted and secured even though it is internal.

## Data at-rest

[AES 256-bit encryption](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) protects all data at rest. Timber is built on top of S3 where all data is encrypted using customer specific keys that rotate every 24 hours.

### Logical data separation

Log data is logically separated throughout it's life-cycle on a per source basis. Each source is provided its own unique namespace during ingestion as well as persistence on S3. It is not possible to access data across multiple sources within a single operation.

### Physical security

Timber operates solely in AWS data centers which are [ISO 9001, ISO 27001, ISO 27017, ISO 27018, PCI DSS Level 1, SEC Rule 17-a-4\(f\), SOC 1, SOC 2, and SOC 3 certified, among many others](https://aws.amazon.com/compliance/). Facilities are protected by 24/7 security staff, bio-metric scanners, and video surveillance.

## User security

### Auth

Timber leverages the excellent [Auth0 service](https://auth0.com/) for securing user accounts. This is Auth0's core competency and ensures Timber's user authentication system is always best-in-class, adhering to the latest security practices and always up-to-date against known vulnerabilities. You can read more about [Auth0's security practices here](https://auth0.com/security/).

### Timber employee account access

#### Access to your account

Only select Timber employees have the ability to access your account directly. This is achieved through Auth0's impersonation feature. Account access cannot be performed without written approval from you first. All attempts to access accounts are logged and performed through temporary sessions that require two-factor authentication.

#### Direct access to your data

Only Timber engineers with validated need can access production data. Security roles and policies separate data access from production system access, allowing Timber to further limit which engineers can access data. Access to any production system is achieved using two-factor authentication.

## Software development life-cycle

Critically important to platform security are the practices and development cycle used to maintain the Timber service. All code changes to the Timber service run through a stringent review process in which security is a required review step before approving.

## Incident response

We subscribe to all security bulletins for the services we use. If an issue is relevant we will seek to resolve it as quickly as possible. If the security incidence affects customers, we will follow industry best practices for disclosure and notification to all of our clients. If you have identified a vulnerability or what more information on specific vulnerabilities please contact us. Your issue will be escalated to our security team.

## Trusting Timber

Beyond implementing best-in-class security practices, cultivating trust with our clients is among the top values at Timber. Security is only as good as the employees that enforce it, and we strive to be open, straight-forward, honest, and operate with integrity. Timber secures the data for thousands of companies across all industries. If you have any additional questions about our security practices please do not hesitate to [contact us](mailto:support@timber.io).

