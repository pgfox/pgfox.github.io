# Extract

How the pipeline pulls raw data out of its sources before any transformation.

- [Extract Commits](extract_commits.md): how `inspect-repo` reads commits from a git clone and writes them as daily parquet files.
- [Extract Pull Requests](extract_pr.md): how `inspect-prs` reads pull requests, reviews and timeline events from the GitHub GraphQL API and writes them as daily parquet files.
- [PR Data Model](PR_data.md): the `pull_requests`, `pr_reviews` and `pr_events` tables and where each column comes from in the GitHub JSON.
- [Extract Jira](extract_jira.md): how `inspect-jira` reads issues and their change history from the Apache Jira REST API and writes them as daily parquet files.
- [Jira Data Model](JIRA_data.md): the `jira_issues` and `jira_events` tables and where each column comes from in the Jira JSON.
