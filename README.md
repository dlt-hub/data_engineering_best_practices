# Data Engineering Best Practices Guide

Data Engineering encompasses a broad range of disciplines aimed at facilitating the collection, storage, processing, and analysis of data. The field is critical for organizations looking to extract valuable insights from their data assets. This guide outlines key areas of focus, providing a structured approach to implementing best practices in data engineering.


# Data Extraction
- Do incremental pipelines where applicable (depends on incremental extraction).
- Use generators for extraction to avoid filling memory. Manage the max memory size used by writing to disk buffers, ensuring scalability of your pipeline.
- Easy local testing - your pipeline, or at least the extractor, should run locally without having to spin up anything heavy.
- Handle errors produced by the source - be mindful of the sources' way of reporting errors - they may be silent.
- Consider implementing "checkpoints" or chunking large extraction jobs to avoid crashes due to network issues.
- Retry network issues or temporary server errors with incremental backoffs.
- Apply data contracts if ingesting data from sources that might send trash (web events for example)
- Make sure to extract data in compliance with privacy laws and maintain security and user consent.
- Respect API rate limits when extracting data from external sources. Implementing rate-limiting strategies prevents overloading source systems and ensures a stable extraction process.
- Document the extraction process and maintain metadata about the data source, extraction time, data volume, and any transformations applied. This aids in traceability and enables you to send lateness alerts for jobs that don't succeed within average times" always good to give specific examples.
- Support flexible data formatting options during extraction to accommodate the needs of downstream processes.
- When possible, capture metadata in rich formats such as Parquet or Avro during extraction. This will enable downstream processes to utilize it.

# Data Normalisation
- Self deploying ddls and automated migrations: Infer your ddls from data or define them and keep them together with the pipeline; A pipeline that self deploys the needed ddl doesn't run the risk of the loading code being out of sync with db state.
- Track schema changes and notify them to the producer and consumer, so the consumer can curate and the producer can react in case of accident.
- Type your data as soon as you can - working with schemaless data is known as "schema on read" and introduces vulnerable assumptions into your code.
- Automate your data types where appropriate - avoid wasting time on speculation and manual upkeep of those assumptions.
- Add database performance hints into your pipeline latest at this step, to enable their automatic deployment to the database.
- Reduce redundancy with normalization, but consider performance impacts.
- When considering between normalisation and de-normalisation, consider trade offs like cost, performance, tool support.
- If new usage patterns emerge in your data, consider optimising its storage for improving access times and cost by adding performance hints where available, such as distribution keys, cluster keys, indices, compression, etc.
- Design efficient data relationships to maintain integrity.
- Consider using referential integrity tests when using normalised data in an analytical setting, as foreign keys are not enforced in analytical technologies for performance reasons.



# Data Loading & Storage
- Re-use glue code in a declarative way. By using a single loader function for all of your pipelines you can more easily maintain good loading behavior.
- Keep the incremental state such as "last value" in a separate table, to avoid expensive full column scans when incrementing data.
- Retry loading in case of network issues; Don't discard your extracted load packages on a failed upload.
- Organize data into partitions based on access patterns, query performance, or chronological order to improve query speed and reduce costs.
- Use caching mechanisms for frequently accessed data to decrease load times and reduce database load.
- Regularly check data integrity and consistency through automated validation checks, ensuring that storage and loading processes haven't introduced errors.
- Implement automated policies for data archiving to manage data lifecycle, ensuring that only relevant data is actively stored and accessed, while older data is archived cost-effectively.
- Adopt scalable storage solutions that can grow with your data needs, ensuring that storage capacity is not a bottleneck.
- Apply data compression to reduce storage requirements and speed up data transfer rates, particularly for large datasets. Use consistent compression types to enable comparing compressed values.
- Establish robust disaster recovery plans, including regular backups and failover mechanisms, to ensure data availability and continuity in case of system failures.


# Data modeling and architecture
- Choose a naming convention and stick to it. Use a linter. People coming after you will appreciate it.
- Understand the domain of the data whether it be houses, trees or sales transactions, form [ubiquotus language](https://martinfowler.com/bliki/UbiquitousLanguage.html) that is agreed upon with the users of the data and use it in your pipeline code and ddl's accordingly. This will make communication about the pipeline and the data it produces easier with the users.
- Document your data model for usage.
- Utilize version control for data models and code to manage changes and facilitate collaboration.

# Data Quality, Governance and documentation
- Document the why; let your code describe the how.
- Make tests in general - end-to-end tests for easy validation of the whole pipeline and unit tests for the business logic to avoid regression.
- Validate the pipeline output - figure out expected values with domain experts, write expectations as tests and catch anomalies before users see them.
- Acknowledge the data quality issues and treat them with relevant importance.
- Collaborate with stakeholders to identify and establish essential data quality conditions tailored to their needs.
- Integrate automated checks to ensure data meets quality standards early on.
- Establish a comprehensive data governance framework that outlines policies, standards, and procedures for data management. This framework should ensure data accuracy, privacy, security, and legal compliance.
- Adopt a continuous improvement mindset for data quality. Implement mechanisms for regular monitoring, reporting, and refining of data quality metrics and processes.
- Maintain detailed metadata for data assets, including descriptions, lineage, and usage guidelines. This enhances data discoverability, usability, and governance.
- Implement feedback loops with data consumers and stakeholders to continually gather insights on data quality, usage, and value. Use this feedback to inform ongoing governance and quality improvement efforts.


# Data Security and Compliance
- Ensure any kept PII data is in accordance with GDPR's right to be forgotten (30d expiration on PII data or some deletion strategy). Ensure any staging areas have deletion policies built-in.
- Manage your pipeline configurations and secrets properly, make sure sensitive information is never sent nor stored in plain text format. If using cloud services, use the provided security services/keyvaults/config managers to keep your secrets safe.
- Implement strong encryption protocols for data at rest and in transit. Use industry-standard encryption algorithms to safeguard data against unauthorized access and ensure confidentiality.
- Establish robust access control policies to restrict data access to authorized personnel only. Implement multi-factor authentication, role-based access control, and least privilege principles to minimize the risk of data breaches.
- Develop and maintain a comprehensive incident response plan to deal with data breaches or security incidents promptly. The plan should include procedures for containment, eradication, recovery, and communication with stakeholders.
- Evaluate the security and compliance practices of third-party vendors who have access to or process your data. Establish contracts and SLAs that mandate adherence to your organization's data security standards.
- Conduct regular training sessions for employees to raise awareness about data security practices, phishing attacks, and the proper handling of sensitive information. Ensure that all personnel understand their role in maintaining data security.
- In addition to GDPR, ensure compliance with other relevant regulations such as CCPA (California Consumer Privacy Act), HIPAA (Health Insurance Portability and Accountability Act), and any industry-specific regulations. Tailor data protection practices to meet the requirements of each applicable law.

  
# Data infrastructure and operations
- Before deploying, conduct extensive testing to identify and resolve issues early, ensuring the system can handle various scenarios. This contributes to the reliability and performance of the data infrastructure.
- Ensure that builds only proceed to deployment after passing integrated tests, reinforcing the stability of the data infrastructure upon updates or new releases.
- Use anonymized real-world data samples for testing to validate that the infrastructure processes data accurately and efficiently, reflecting actual operational conditions. 
- Monitor costs and limits to prevent accidental overruns. It's easy for expenses to skyrocket, especially when querying large datasets.

- Implement secure and efficient data storage, processing, and access mechanisms, ensuring compliance with data protection regulations. This includes practices for data ingestion, encryption, and backup strategies.
- Establish robust monitoring to detect and address performance bottlenecks or anomalies in real time. Ensure the infrastructure is scalable to adapt to varying data volumes and operational demands.

These categories represent the field of data engineering and should be sufficient to encompass any best practices. 
If you wish to add more, please check our contribution guide and consider keeping the existing structure. 
