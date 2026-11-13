# Website Hosting and Application Architecture Patterns on AWS

**Category:** AWS Services and Cloud Architecture  
**Document Type:** Learning Reference  
**Date:** 13 November 2026  
**Status:** Learning Note

## 1. Purpose

This document compares common AWS patterns for hosting a company website, portfolio, WordPress site, or a small web application.

The goal is not to choose the most powerful service. It is to select the simplest architecture that satisfies the application's technical, operational, security, and business requirements.

> Important distinction: Amazon RDS is a managed relational database service, not a website hosting service. A website or application needs a hosting or compute layer in addition to RDS.

## 2. Pattern A — Static Website with Amazon S3 and CloudFront

**Typical use cases**
- Company profile and services website
- Personal portfolio
- Landing page
- Documentation or project showcase
- Any website that can be delivered as static files

**Main components**
- **Amazon S3:** Stores website files such as HTML, CSS, JavaScript, images, and documents.
- **Amazon CloudFront:** Acts as the content delivery network (CDN), delivering content from edge locations and providing features such as HTTPS and caching.
- **Optional Amazon Route 53:** Manages DNS for a custom domain.

**Request flow**

Visitor → CloudFront → Amazon S3 origin

**Why choose this pattern?**
- No web server or operating-system administration is required.
- It is a good fit for websites that do not need server-side processing.
- CloudFront can improve content delivery performance and add an HTTPS-enabled delivery layer.
- S3 provides durable object storage for website assets.

**Limitations and considerations**
- A purely static site cannot directly perform server-side operations such as processing a user login or writing an order to a database.
- Dynamic features need an additional backend, such as an API and AWS Lambda, or an application hosted elsewhere.
- For a secure S3-backed CloudFront design, use Origin Access Control (OAC) so that users access the content through CloudFront rather than directly through the bucket.

**When this is a good choice:** A company website or portfolio that mainly presents information and does not need a custom server-side application.

Official documentation: [Hosting a secure static website with S3 and CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/getting-started-secure-static-website-cloudformation-template.html).

## 3. Managed Hosting Alternative — AWS Amplify Hosting

Amplify Hosting is an alternative way to build and host supported web applications without manually assembling every hosting component.

**Typical use cases**
- Static websites connected to a Git repository
- Front-end applications built with supported frameworks
- Supported server-side-rendered (SSR) web applications

**Main capabilities**
- Git-based deployment workflow
- Build and deployment automation
- Hosting on the AWS content delivery network
- Custom domain and HTTPS support

**Important distinction:** Amplify Hosting is a managed hosting and deployment service. It uses AWS's global content delivery network, powered by CloudFront, but it is not simply another name for a manually configured CloudFront distribution.

**When this is a good choice:** You want a straightforward Git-based workflow to build and publish a supported web application, with less hosting infrastructure to configure manually.

Official documentation: [AWS Amplify Hosting](https://docs.aws.amazon.com/amplify/latest/userguide/).

## 4. Pattern B — WordPress on Amazon Lightsail

**Typical use cases**
- Company website managed through WordPress
- Blog or news website
- Website where non-developers need to publish and edit content
- Small business website using WordPress themes and plugins

**Main components**
- **Amazon Lightsail:** Provides a simplified virtual private server (VPS) environment with predictable instance plans.
- **WordPress Blueprint:** Allows you to launch an instance with WordPress preinstalled.
- **WordPress database:** WordPress requires a database to store posts, pages, users, settings, and other content. Depending on the design, this can be hosted with the WordPress instance or moved to a separate managed database.
- **Optional Lightsail features:** Static IP, DNS, snapshots, managed databases, and load balancing, depending on the requirements.

**Request flow**

Visitor → Lightsail WordPress instance → WordPress application and database

**Why choose this pattern?**
- WordPress can be launched without manually installing every component.
- The management experience is simpler than assembling a custom EC2 environment.
- It is suitable for small websites with conventional WordPress requirements.

**Limitations and considerations**
- WordPress is a dynamic application and does use a database; it is not the same as a database-free static website.
- Plugins, themes, WordPress updates, security, backups, and performance still need attention.
- More demanding workloads may require a separate managed database, CDN, load balancer, or a more flexible architecture.

**When this is a good choice:** You want a content-managed website and prefer WordPress over building a custom application.

Official documentation: [Launch and configure a WordPress instance on Lightsail](https://docs.aws.amazon.com/lightsail/latest/userguide/amazon-lightsail-quick-start-guide-wordpress.html).

## 5. Pattern C — Application Hosted on a Single EC2 Instance

**Typical use cases**
- Small custom web application
- Learning or proof-of-concept environment
- Internal tool with modest requirements
- Application that does not require a separate relational database
- Application that uses an external API or another data store

**Main components**
- **Amazon EC2:** Provides the virtual server where the operating system, web server, runtime, and application are installed.
- **Optional local storage:** Stores application files or temporary data.
- **Optional local database:** A database such as MySQL or PostgreSQL can be installed on the same EC2 instance, but it becomes self-managed by the project owner.

**Request flow**

Visitor → EC2 instance → Web server and application

If a local database is installed:

Visitor → EC2 instance → Application → Local database on the same instance

**Why choose this pattern?**
- Gives you control over the operating system and software environment.
- Can be useful for learning Linux, web server configuration, security groups, deployment, and application operations.
- May be sufficient for a small prototype or application with no database requirement.

**Limitations and considerations**
- You are responsible for operating-system updates, application deployment, server security, monitoring, and backups.
- If the database is installed on the same EC2 instance, you are also responsible for database installation, patching, backup, recovery, and high availability.
- Combining the application and database on one server creates a shared failure and resource boundary. CPU, memory, disk, or maintenance problems can affect both.
- A single EC2 instance is not automatically a highly available architecture.

**When this is a good choice:** You need direct server control, the workload is small or experimental, and the operational responsibilities are understood and acceptable.

## 6. Pattern D — Application on EC2 with Amazon RDS

**Typical use cases**
- Custom website with user accounts
- Customer portal
- Booking or order-management application
- CRM or internal business application
- Application that needs a relational database

**Main components**
- **Amazon EC2:** Runs the web application and its business logic.
- **Amazon RDS:** Provides a managed relational database such as MySQL or PostgreSQL.
- **Amazon VPC and security groups:** Control network connectivity between the application and database tiers.
- **Optional Application Load Balancer and multiple EC2 instances:** Can be added when availability or scaling requirements justify them.

**Request flow**

Visitor → EC2 application → Amazon RDS

**Why choose this pattern?**
- Separates the application tier from the relational database tier.
- RDS manages many routine database infrastructure tasks, such as database software installation, backups, and patching, depending on the selected configuration.
- The application and database can be scaled and operated more independently.
- RDS provides managed capabilities for backups, monitoring, and high availability options.

**Limitations and considerations**
- RDS does not host the website or run the application's business logic; it stores and serves database data.
- The application still needs a compute or hosting layer, such as EC2, containers, Lambda, or another platform.
- The customer remains responsible for data modeling, query design, database access control, and application-level performance.
- A separate RDS instance adds cost and configuration work, so it should be justified by the application requirements.

**Security baseline**
- Prefer placing the RDS database in private subnets.
- Do not allow unrestricted public database access.
- Configure the database security group to accept traffic only from the application tier's security group on the required database port.
- Store database credentials securely and grant the application only the permissions it needs.

**When this is a good choice:** The application requires a relational database and the team prefers a managed database service instead of operating the database on EC2.

Official documentation: [What is Amazon RDS?](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/).

## 7. Where CloudFront Fits

CloudFront is a CDN and delivery layer, not a standalone website origin.

It can sit in front of different origins, including:
- An Amazon S3 bucket
- An Application Load Balancer
- An HTTP server running on EC2
- Another supported custom HTTP origin

For example:
- Static website: Visitor → CloudFront → S3
- Dynamic application: Visitor → CloudFront → Application Load Balancer → EC2
- Amplify Hosting: Amplify manages the hosting and deployment workflow and delivers the application through the AWS CDN.

CloudFront can therefore complement several architectures; it does not replace the application server or database.

Official documentation: [CloudFront origins](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/DownloadDistS3AndCustomOrigins.html).

## 8. Quick Comparison

| Pattern | Suitable workload | Hosting / compute | Database |
|---|---|---|---|
| S3 + CloudFront | Static company site or portfolio | S3 + CloudFront | Not required for static content |
| Amplify Hosting | Supported front-end or full-stack web app | Amplify Hosting | Depends on the application |
| WordPress on Lightsail | Content-managed WordPress site | Lightsail WordPress instance | Required by WordPress; deployment-dependent |
| Single EC2 | Small custom app or server-controlled workload | EC2 | None, external, or self-managed locally |
| EC2 + RDS | Dynamic application with relational data | EC2 | Managed relational database on RDS |

## 9. Simple Decision Guide

1. **Is the website only informational and made of static files?**  
   Consider S3 + CloudFront, or Amplify Hosting for a Git-based managed workflow.

2. **Do you want to manage the website through WordPress?**  
   Consider Amazon Lightsail with a WordPress Blueprint.

3. **Do you need a custom application but no separate relational database?**  
   Consider EC2 alone, or another suitable compute service. Add a database only if the application needs one.

4. **Does the custom application need a relational database?**  
   Consider separating the application tier and database tier with EC2 + RDS.

5. **Do you need a CDN for performance and delivery?**  
   Consider CloudFront in front of a suitable origin. It can complement, rather than replace, the hosting architecture.

## 10. Key Learning Points

- EC2 is a compute service. It runs operating systems, web servers, and applications.
- RDS is a managed relational database service. It is not a website hosting service.
- S3 can store static website assets; CloudFront can deliver them globally.
- Amplify Hosting simplifies the build, deployment, and hosting workflow for supported web applications.
- Lightsail simplifies launching common workloads such as WordPress.
- EC2 can run an application without RDS. A database is needed only when the application's data requirements call for one.
- Managed services reduce some operational work, but they do not remove the need to design for security, availability, performance, and cost.

## 11. Official AWS References

- [Amazon CloudFront Documentation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html)
- [Secure Static Website with S3 and CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/getting-started-secure-static-website-cloudformation-template.html)
- [AWS Amplify Hosting Documentation](https://docs.aws.amazon.com/amplify/latest/userguide/)
- [Amazon Lightsail WordPress Guide](https://docs.aws.amazon.com/lightsail/latest/userguide/amazon-lightsail-quick-start-guide-wordpress.html)
- [Amazon RDS User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)
