# Data Engineering Best Practices Guide

Data Engineering encompasses a broad range of disciplines aimed at facilitating the collection, storage, processing, and analysis of data. The field is critical for organizations looking to extract valuable insights from their data assets. This guide outlines key areas of focus, providing a structured approach to implementing best practices in data engineering.


# Data Extraction
- Do incremental pipelines where applicable (depends on incremental extraction)
- Use generators for extraction to avoid filling memory. Manage the max memory size used by writing to disk buffers, ensuring scalability of your pipeline.
- Easy local testing - your pipeline, or at least the extractor, should run locally without having to spin up anything heavy.
- Handle errors produced by the source - be mindful of the sources' way of reporting errors - they may be silent.
- Consider implementing "checkpoints" or chunking large extraction jobs to avoid crashes due to network issues.
- Retry network issues or temporary server errors with incremental backoffs.
- Apply data contracts if ingesting data from sources that might send trash (web events for example)


# Data Normalisation
- Self deploying ddls and automated migrations: Infer your ddls from data or define them and keep them together with the pipeline; A pipeline that self deploys the needed ddl doesn't run the risk of the loading code being out of sync with db state.
- Track schema changes and notify them to the producer and consumer, so the consumer can curate and the producer can react in case of accident.
- Type your data as soon as you can - working with schemaless data is known as "schema on read" and introduces vulnerable assumptions into your code.
- Automate your data typing where sensible - do not waste time guessing and manually maintaining those guesses.
- Add database performance hints into your pipeline latest at this step, to enable their automatic deployment to the database.


# Data Loading & Storage
- Re-use glue code in a declarative way. By using a single loader function for all of your pipelines you can more easily maintain good loading behavior.
- Keep the incremental state such as "last value" in a separate table, to avoid expensive full column scans when incrementing data.
- Retry loading in case of network issues; Don't discard your extracted load packages on a failed upload.

# Data modeling and architecture
- Choose a naming convention and stick to it. People coming after you will appreciate it.
- Understand the domain of the data whether it be houses, trees or sales transactions, form [ubiquotus language](https://martinfowler.com/bliki/UbiquitousLanguage.html) that is agreed upon with the users of the data and use it in your pipeline code and ddl's accordingly. This will make communication about the pipeline and the data it produces easier with the users.
- Document your data model for usage

# Data Quality, Governance and documentation
- Document the why; let your code describe the how.
- Make tests in general - end-to-end for easy checks if everything works and unit tests for the business logic
- Validate the pipeline output - figure out expected values with domain experts, write expectations as tests and catch anomalies before users see them.

# Data Security and Compliance
- Ensure any kept PII data is in accordance with GDPR's right to be forgotten (30d expiration on PII data or some deletion strategy). Ensure any staging areas have deletion policies built-in.
- Manage your pipeline configurations and secrets properly, make sure sensitive information is never sent nor stored in plain text format. If using cloud services, use the provided security services to manage secrets.
  
# Data infrastructure and operations



These categories represent the field of data engineering and should be sufficient to encompass any best practices. 
If you wish to add more, please check our contribution guide and consider keeping the existing structure. 
