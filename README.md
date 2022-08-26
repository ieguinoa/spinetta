

The client service runs on each execution node and takes care of:
- Store configuration of local schedulers/custom runners (i.e load Galaxy-like runners configuration)
- Implement a simple local job runner.
- Measuring and reporting server load
- Measuring and reporting latency against object stores.


The server fetches the job requests and delegates the job to one of the executors based on:
- Sensitive data configuration: Whether the job is constrained to run in a specific job execution group (one or a set of execution nodes) that fullfil the clearence criteria.
- The source and destination object stores: Using (cached) information aboutproximity/latency between execute nodes and source and/or destination object stores. 
- The reported server load: cached or real-time assesment.
- Execution nodes tags/groups listed in the job definition.

TODO features:
    - Reservation requests: reserve a slot in a job execution group. The caller gets a reservation id with an expiration date that can use to submit jobs in the future.
    - Transactions like execution: sets of jobs that need to be allocated all together in the same execution node/group.
    - Accounting/billing

