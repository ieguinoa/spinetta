Spinetta is data locality aware meta-scheduler aimed at centrilizing the feature-based decision of where to dispatch scheduled jobs, selecting from a pool of heterogeneous execution nodes.
Heavily influenced by DIRAC (http://lhcb-comp.web.cern.ch/Frameworks/DataManagement/Documents/Papers/DIRAC.pdf) but developed to distribute work based on Galaxy-like job features and designed to schedule jobs with large data input and outputs by prioritizing data locality.
The mode of scheduling is still a pull model but the set of features analized to start a pull can be changed. Mainly: Executors are administered by local site administrators, and they can be shared with other submission sources but Spinetta will not be used just for backfilling. The local agent can be configured to pull jobs in different cases, even when there is no free slots to run jobs:
    - Pull jobs pp to a local load of L = (100 + x)% , where x can be > 0 (for X=0 the local agents will only fetch jobs when there is unused)
    - Set a quota based on job number/rate: pull jobs up to a total rate of Y jobs per/day. This can be combined with a local load limit, or just set the X limit to `inf` and the agent will pull jobs at this rate.
    - Pull jobs based on a quota fixed on the job requirements: [total memory | total cpus | total walltime ] per day. 

The server fetches the job requests and delegates the job to one of the executors based on:
- Sensitive data configuration: Whether the job is constrained to run in a specific job execution group (one or a set of execution nodes) that fullfil the clearence criteria.
- The source and destination object stores: Using node-reported information about proximity/latency between execute nodes and source and/or destination object stores. 
- The execution nodes capabilities: type of dependency resolution (docker, conda, preinstalled modules), workflow based jobs (i.e CWL), and .
- Matches between job input dataset and cached datasets in worker nodes (datasets fetched by previous jobs in that node). 
- Matches between job input dataset and resolved dependencies in worker nodes (predownloaded images, prebuild conda environments).
- The reported server load: cached or real-time assesment.
- Execution nodes tags/groups listed in the job definition.

The client agent runs on each execution node and is expected to run with little effort from local site administrators, either for installation or maintenance.
- Store configuration of local schedulers/custom runners (i.e load Galaxy-like runners configuration)
- Implement a simple local job runner: Can execute Docker based 
- Measuring and reporting server load
- Measuring and reporting latency against object stores.


The module defines it's own tasks 
TODO features:
    - Reservation requests: reserve a slot in a job execution group. The caller gets a reservation id with an expiration date that can use to submit jobs in the future.
    - Transactions like execution: sets of jobs that need to be allocated all together in the same execution node/group.
    - Accounting/billing

