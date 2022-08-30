Spinetta is a meta-scheduler aimed at centrilizing the feature-based decision of where to run the scheduled jobs, out of a pool of available execution nodes.
Heavily influenced by DIRAC (http://lhcb-comp.web.cern.ch/Frameworks/DataManagement/Documents/Papers/DIRAC.pdf) but developed to distribute work based on Galaxy-like job features

The server fetches the job requests and delegates the job to one of the executors based on:
- Sensitive data configuration: Whether the job is constrained to run in a specific job execution group (one or a set of execution nodes) that fullfil the clearence criteria.
- The source and destination object stores: Using node-reported information about proximity/latency between execute nodes and source and/or destination object stores. 
- The execution nodes capabilities: type of dependency resolution (docker, conda, preinstalled modules), workflow based jobs (i.e CWL).
- Matches between task input dataset and cached datasets in worker nodes (datasets fetched by previous jobs in that node).
- The reported server load: cached or real-time assesment.
- Execution nodes tags/groups listed in the job definition.

The client service runs on each execution node and is expected to run with little effort from local site administrators, either for installation or maintenance.
- Store configuration of local schedulers/custom runners (i.e load Galaxy-like runners configuration)
- Implement a simple local job runner: Can execute Docker based 
- Measuring and reporting server load
- Measuring and reporting latency against object stores.




The module defines it's own tasks 
TODO features:
    - Reservation requests: reserve a slot in a job execution group. The caller gets a reservation id with an expiration date that can use to submit jobs in the future.
    - Transactions like execution: sets of jobs that need to be allocated all together in the same execution node/group.
    - Accounting/billing

