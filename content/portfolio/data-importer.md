---
title: Data Importer
date: 2025-04-27
images:
- https://cdn.cmsfly.com/665b46e391fd6b0012961a89/images/Group-5-(1)-F-d6N.png
description: An embedded ETL tool for importing data into SaaS applications.
---

### What's the problem with current data tooling?

Currently 'ETL' (extract, transform, load) is a job that is done by Data Engineers between a source and a target system. Usually the target system is is a data warehouse such as Snowflake, BigQuery, or Redshift.

But non technical users also perform 'ETL' tasks all the time. Marketing teams import data into their product management tool to build reports. Sales teams import data into their CRM to enrich their pipeline. Operations teams import order data into work management tools to manage their supply chain.

However, most ETL tooling (e.g. Airbyte, Fivetran, etc.) is not designed for these use cases. They are designed to be bulk imported into a data warehouse, and then Data Engineers build upstream pipelines to other tools (usually dashboards rather than other SaaS applications).

Data Importer is an 'embedded ETL tool' that allows users to import data into SaaS applications in a way that is more natural and intuitive.

### What is embedded ETL?

Embedded ETL is where the ETL process is embedded within the user's workflow/target application. The user doesn't need to think about the ETL process, they just need to import the data in a way that is seamless and works with the target application.

My vision for Data Importer is to have many 'clients' (e.g. marketplace apps or deep UI integrations) for target SaaS applications (project management tools, no-code database tools, CRMs etc.) with a single set of data sources. 

The first client is launched into the monday.com app marketplace and has been installed by over 1000 companies with 50+ paying customers.
