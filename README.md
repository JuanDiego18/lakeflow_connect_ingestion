# lakeflow_connect_ingestion
Repo for setting up start/stop of a Ingestion Gateway in Databricks Lakeflow Connect.

Ingestion Gateway is setup to continuously run to fetch changes from the source database. This is by design, the ingestion gateway must run in continuous mode to avoid changes being dropped due to log retention. If changes have been dropped, a full refresh is required for all tables. 

Use this job if you want to have full control over the ingestion gateway scheduling, but you need to take care of the following **important considerations**:

- **CDC retention**: Make sure the gateway is stopped for less than the CDC log retention period (default 3 days). If stopped longer, you'll need a full refresh.
- **Warm-up time**: The gateway needs time after starting to catch up on accumulated changes. Start it well before anyone needs fresh data.
- **Snapshot first**: Let the initial pipeline run complete its full snapshot before setting up the start/stop schedule. Watch the event log for completion. 
- **Trial and error on Z**:The amount of time the gateway needs to run depends on data volume. Start with a generous window (e.g., 6 AM - 8 PM) and shrink it once you understand the catch-up time.
