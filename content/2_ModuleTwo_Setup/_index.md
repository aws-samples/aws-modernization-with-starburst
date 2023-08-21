---
title: "Query federation with Starburst Galaxy" # MODIFY THIS TITLE IF APPLICABLE
chapter: true
weight: 2
---

# Query federation with Starburst Galaxy

## Query Federation workshop architecture

For this workshop, analyze one of the datasets in the COVID-19 data lake and run interactive queries to discover the proper insight required to federate this data with the TPC-H dataset. Then, write a SQL query with both data sources to find the total case count by region.

*   The first dataset is the [Global Coronavirus (COVID-19) Data](https://aws.amazon.com/marketplace/pp/prodview-vtnf3vvvheqzw?sr=0-1&ref_=beagle&applicationId=AWSMPContessa#offers) provided by Enigma. This dataset tracks confirmed cases.
*   The second dataset is the standard [TPC-H](https://docs.starburst.io/starburst-galaxy/catalogs/tpch.html) dataset which provides the region information.

This guide walks you through:

*   Connecting Starburst Galaxy to AWS
*   Querying multiple data sources

Want to see it in action? Watch a [video](https://docs.starburst.io/videos/2023-03-06-sg-query-federation-tutorial.html) that demonstrates this workshop.