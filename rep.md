# Retail Energy Provider

Retail Electric Providers (REPs) in Texas are the companies that buy electricity in bulk from the wholesale market and sell it directly to residential and commercial customers. Here's what they do:

## Core Business Function

REPs act as the intermediary between the wholesale ERCOT electricity market and end consumers. They don't own power plants or transmission lines — they're essentially energy merchants and service companies.

### Key Responsibilities

* Procurement — Purchase electricity on the ERCOT wholesale market (day-ahead and real-time) or via long-term power purchase agreements (PPAs) with generators.
* Risk Management — Hedge against price volatility using financial instruments so they can offer stable fixed-rate or variable-rate products to customers.
* Customer Enrollment — Sign up customers by ESI ID (the unique identifier for each meter/service point in Texas), submit enrollment transactions to TDSPs via EDI.
* Billing — Issue monthly bills that combine their energy charges with pass-through TDSP delivery charges, ERCOT fees, and applicable taxes.
* EDI Transaction Processing — Exchange standardized transactions (814 enrollments, 650 service orders, 867 usage data, 810 invoices) with TDSPs following Texas SET protocols.
* Regulatory Compliance — Maintain PUCT registration, file required disclosures (EFL, Terms of Service, YRAC), follow §25.475 and §25.479 rules on pricing transparency and billing format.
* Customer Service — Handle inquiries, disputes, move-ins/move-outs, and payment arrangements.

### Product Design

REPs differentiate themselves by offering different rate structures — fixed-rate contracts, variable/indexed plans, green energy plans, prepaid electricity, and time-of-use rates. This is the primary competitive dimension in the deregulated Texas market.

### What They Don't Do

They don't own or operate the wires (that's the TDSPs — Oncor, CenterPoint, AEP, etc.) or the grid itself (that's ERCOT). If your power goes out, you call the TDSP, not your REP. It's a capital-light but margin-thin business where volume, churn management, and smart hedging are everything

# Retail Electric Platform

## Personas
* Customer - a customer will sign up for service through the public enrollment website. The customer will then be able to login to the customer portal and manage all of their information such as viewing contracts, their pricing, monitor their usage, view their bills, make payments and also submit service requests for the administrators.
* Administrator - an employee of the retail energy provider will manage the company using the administrator portal. The administrator portal will allow them to see all the customer information, but also be able to view aggregatesd views and reports, manage pricing and products, and perform all the administration tasks needed to manage the company.
* Developer - the developer will be able to perform all the activities associated with maintaining and enhancing the projects and system configurations.
* Operator - the operator will be able to perform maintainance and implement enhancement requests based on requests from the development teams and administrators.

## Data Flows

The web projects are served from web servers running in the workload clusters. The web projects interact only with the API gateways and authentication/authorization (authz) services to access data from the backend services, all running in the workload clusters. The API gateways will interact with the API projects that will in turn update the databases and create new events corresponding to the activities engaged.

## Projects

Three web projects: 

* web-public
* web-customer
* web-administrator

Nine API projects implemented using [Quarkus](https://quarkus.io), [PostgreSQL](https://www.postgresql.org) for databases, and [Streams for Apache Kafka](https://docs.redhat.com/en/documentation/red_hat_streams_for_apache_kafka/latest) for events

* api-reference - ISO, TDSP, Zip code mappings
* api-crm - Customer (CustomerIndividual, CustomerBusiness), Account
* api-service-point - ServicePoint and Premise
* api-product - Rate plans, EFL and other documents
* api-pricing - Pricing for ServicePoints at any given time
* api-enrollment - Contracts
* api-usage - Usage data based on ServicePoints
* api-billing - Bills, Payments
* api-notification - Owns all outbound customer and administrator communications (emails, SMS)

One infrastructure-as-code project using [Ansible](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/latest)

* iac

Three gitops projects used by [OpenShift Gitops](https://docs.redhat.com/en/documentation/red_hat_openshift_gitops/latest)

* gitops-hub-cluster
* gitops-workload-cluster
* gitops-workload-applications

## Services and Technologies

* Web projects based on [Angular](https://angular.dev) and [Bootstrap](https://ng-bootstrap.github.io/#/home)
* Hub and Workload Kubernetes clusters provided by [Red Hat OpenShift Container Platform](https://docs.redhat.com/en/documentation/openshift_container_platform/latest)
* Cluster management will be provided by [Red Hat Advanced Cluster Management for Kubernetes](https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_management_for_kubernetes/latest)
* Cluster Security will be provided by [Red Hat Advanced Cluster Security for Kubernetes](https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_security_for_kubernetes/latest)
* Cluster manifests will be managed using [Red Hat OpenShift Gitops](https://docs.redhat.com/en/documentation/red_hat_openshift_gitops/latest)
* Authentication and Authorization uses [Red Hat build of Keycloak](https://docs.redhat.com/en/documentation/red_hat_build_of_keycloak/latest)
* Events and Streaming uses [Streams for Apache Kafka](https://docs.redhat.com/en/documentation/red_hat_streams_for_apache_kafka/latest)
* API Gateways will be managed using [Red Hat Connectivity Link](https://docs.redhat.com/en/documentation/red_hat_connectivity_link/latest)
* CICD pipelines will be implemented using [Red Hat OpenShift Pipelines](https://docs.redhat.com/en/documentation/red_hat_openshift_pipelines/latest) and [Red Hat Trusted Application Pipeline](https://docs.redhat.com/en/documentation/red_hat_advanced_developer_suite_-_software_supply_chain/latest)
* Internal Developer Portal will be implemented with [Red Hat Developer Hub](https://docs.redhat.com/en/documentation/red_hat_developer_hub/latest)
