---
title: "Technical Issue / Problem" # MODIFY THIS TITLE
chapter: true
weight: 2 # MODIFY THIS VALUE TO REFLECT THE ORDERING OF THE MODULES IF APPLICABLE
---

What is data federation?
------------------------

Data federation involves the creation of a virtual database that maps an enterprise’s many different sources and makes them accessible through a single interface. Unlike other technologies, data federation leaves all data at the source. Integration happens in real time as data consumers apply one query across multiple sources.

**Related reading:** [How does data federation work?](https://www.starburst.io/blog/how-does-data-federation-work/)

What are the benefits of data federation?
-----------------------------------------

It’s not unusual for companies to have hundreds of data repositories. As companies grow and evolve, their storage infrastructure naturally becomes more heterogeneous, and company data becomes more difficult to access. Some of the challenges of integrating fragmented enterprise data landscapes include:

**Proprietary query formulations**: Vendors implement query tools specific to their solutions. Even when vendors use their own flavors of [SQL](https://www.starburst.io/learn/data-fundamentals/sql/), data access requires users to understand how each data source expects queries to look.

**Proprietary extensions**: Vendors offer extensions that add capabilities or improve performance — but only within the context of their solution. Again, data engineers must understand how a data source’s vendor-specific extensions impact data extraction.

**Semantic variations**: When organizational domains implement data systems, they make design decisions that make sense at the time and within their context. Data users trying to access this data do not have that perspective. As a result, source-to-source variations in semantics, formats, and other data properties complicate cross-domain data integration.

A federated [data architecture](https://www.starburst.io/learn/data-fundamentals/data-architecture/) resolves these challenges by masking inconsistencies within an abstraction layer. Users can run queries within this virtual layer without worrying about technologies or the structure of source data. This [virtualization](https://www.starburst.io/learn/data-fundamentals/data-virtualization/) of an enterprise’s data infrastructure leads to five core benefits of data federation:

### 1\. Real-time access

Traditionally, [data analytics](http://starburst.io/learn/data-fundamentals/data-analytics/) took time. Users needed help from data teams to overcome their company’s fragmentation challenges. [Data engineers](https://www.starburst.io/learn/data-fundamentals/data-engineering/) had to develop extract, transform, and load (ETL) and extract, load, and transform (ELT) pipelines to copy, prepare, and load it into a new dataset.

This time-consuming development process extended the span between business questions and insights, slowing the company’s decision-making. The proliferation of [data warehousing](https://www.starburst.io/learn/data-fundamentals/data-warehouse/) is symptomatic of a company’s struggles to shorten time to insight.

Data federation empowers end users with real-time access to data across the organization. Leaving data at the source and creating a virtual data consumption layer makes [pipelines](https://www.starburst.io/learn/data-fundamentals/data-pipeline/) and interim datasets unnecessary. Users no longer need help from data teams. They can run queries themselves using the analytical and business intelligence tools they already know.

Democratized, real-time access speeds time to insight and makes decision-making more effective.

### 2\. Data integration

Machine learning algorithms and artificial intelligence applications can yield the most profound business insights. However, data scientists can only produce innovative data products with ready access and reliable [data quality](https://www.starburst.io/learn/data-fundamentals/data-quality/). When domains and proprietary systems silo data, it takes enormous effort by the data teams to extract, cleanse, and prepare the large datasets data scientists require.

By unifying the organization’s disparate data sources behind a data consumption layer, data federation streamlines the integration of large datasets. Scientists can quickly run queries as they iteratively explore subsets of the company’s vast data stores. With a better understanding of the data landscape, data scientists can present engineers with more refined requirements for integrating exponentially larger datasets.

### 3\. Reduce costs

Fragmented data infrastructures make data analytics more expensive. Companies must invest extra storage capacity to support interim datasets and new databases. Data warehouses promise to consolidate the data that matters, but the old data sources always seem to stay.

Less visible, but just as important, companies must accept lower productivity from their data teams. Developing and maintaining data pipelines is time-consuming and limits how accessible data teams are to the rest of the organization.

Data federation reduces these costs. Leaving data at the source avoids the dedicated databases and the proliferation of data warehouses that drive escalating storage costs.

Additional savings arrive indirectly when federation frees data teams from less productive tasks. They no longer maintain catalogs of ETL and ELT pipelines. Data democratization means engineers are not distracted by simple query requests. As a result, data teams have more time to support complex projects that can drive business innovation.

### 4\. Scalability

One reason for the escalating costs of big data analytics is the just-in-case investment companies must make to ensure the storage and compute capacity is there when analysts need it. Underutilized capacity ties up cash the company could allocate to more productive uses.

Data federation leverages cloud economics to decouple storage and compute. IT departments can plan for steady growth in storage capacity and develop a data infrastructure with the optimal balance of performance, productivity, and cost.

Rather than over-investing to manage variations in compute demand, data federation lets companies scale on-demand compute capacity.

### 5\. Flexibility

Fragmented data infrastructures are brittle and resistant to change. For instance, any glitch in a data migration project could disrupt operations for days. The reason for this inflexibility is the way data use cases are inextricably linked to data infrastructure. Companies design data products around how each source stores and structures data. A change at the source ripples through these dependencies in unanticipated ways.

By abstracting sources within a data consumption layer, federation eliminates these dependencies. Changes at the source happen transparently to business users.

For example, most users will never know when a migration project moves data from an on-premises system to the cloud. One day, their queries in the federated consumption layer pull data from the old system. The next day, their queries pull data from the new system.
