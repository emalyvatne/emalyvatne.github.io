---
title: National Security Innovation Network X-Force Fellowship Reflection
author: 'Emaly Vatne'
date: '2023-08-15'
slug: NSIN-XFF-reflection
categories: []
subtitle: ''
summary: 'In this post, I reflect on my 10-week X-Force Fellowship sponsored by the National Security Innovation Network. During my fellowship, I was placed with the Army National Guard focusing on data engineering research for the Holistic Health and Fitness (H2F) system.'
authors: []
lastmod: '2023-08-15T20:57:45-05:00'
projects: []
---

The X-Force Fellowship is a summer internship program sponsored by the National Security Innovation Network and places students with DoD problem sponsors and projects. The projects vary widely, but I was placed with Army Nationaly Guard and the Holistic Health and Fitness (H2F) system. H2F is a strategic initiative to maximize soldier readiness in five domains: sleep, nutrition, physical, mental, and spiritual.

Since my background is in sport science in the context of athletics, I am familiar with the circle of care that athletes receive. Teams generally are supported by a team physician, athletic trainer, sport scientist, dietitian, strength and conditioning coach, and a sport psychologist/mental performance coach. The H2F system aims to bring similar support to soldiers to ultimately enhance their performance and optimize their health and longevity.

One challenge that the Army National Guard has faced is an uncentralized model for the storage locations of data that are related to H2F. In the context of athletics, teams often utilize a third-party database, or 'athlete management system', to handle commercial API calls, data storage, simple analysis, and visualization/reporting. Army National Guard, however, has data stored in multiple different systems that do not speak/transfer data to one another. This presents a major roadblock for understanding relationships and deriving meaningful insights from the H2F data. Thus, the problem statement of the 2023 X-Force Fellowship I was assigned to was to investigate if it would be feasible for Army National Guard to create a centralized repository for data and resources related to H2F.

In order to answer this question, the current 'systems' or data storage locations were evaluated to further understand the current problem. The next step, and the bulk of the summer internship, was spent investigating athletics and enterprise approaches to centralized data storage and aggregation. 

In our final deliverable presentation, we included three different potential approaches to solve the need for a centralized data repository for Army National Guard and H2F. Our first two approaches were based on previous work in the DoD and in team sport athletics, and our final approach was based on data engineering best practices and approaches taken by large, industry-leading organizations. For each approach, the final deliverable included an in-depth review of the platform, strengths, limitations, return on investment for Army National Guard H2F, and perceived potential gaps based on our understanding of the problem. 

For the context of this discussion, I wanted to discuss our third or suggested approach for solving the original problem statement. One thing we noted during our interviews with employees of the Army National Guard is that they all noted being familiar with and getting licenses for Power BI. Power BI is a Microsoft tool for data visualization and some analysis. Power BI also naturally integrates with other data/statistical analysis tools like R and Python to add in all of the features of these tools to the platform. This note was coupled with naturally noticing that DoD meets via DoD instances of Microsoft Teams. Further research uncovered that Microsoft has specific configurations and approvals for DoD service. Based on these recommendations, I began investigating if Microsoft tools could build this centralized data repository and serve all of the needs of a data lifecycle from data creation/ingestion through storage, analysis, and to reporting/visualization. 

I had become interested in data engineering in general a few months before this project began. I listened to a very interesting presentation of a practitioner who built data pipelines to automate and execute API calls, data cleaning, storage, transform (or the ELT pipeline) and reporting using Databricks and Amazon Web Services. After this presentation, I began learning more about these tools and Python for data engineering and data pipelines. This personal interest became especially valuable during the X-Force Fellowship and for our third approach. Being familiar with concepts like data lakes, data warehouses, data lakehouses, Databricks, medallion data lakehouse architecture, and APIs was critically useful as we began investigating these services and functionalities on the Microsoft Enterprise. 

Further research of how large organizations and businesses handle their data pipelines and engineering uncovered that this is common in other domains. Further, data architecture diagrams for large organizations, while the original data sources are different for Army National Guard H2F, also helped inform our summer deliverable product.

Overall, the 2023 X-Force Fellowship was a great experience to translate my interests and experiences in exercise and sport science to a real-world DoD problem. Further, I had the opportunity to work with wonderful teammates and project sponsors who made the project even more enjoyable. I have also been fortunate enough to have continued to work with Army National Guard H2F through an indepedent study during the Autumn 2023 semester. This fellowship has introduced me to a whole new domain to apply my interests and way to serve a new group of people and I look forward to future collaborations and opportunities.
