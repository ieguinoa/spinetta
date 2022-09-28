Spinetta is data locality aware meta-scheduler aimed at centrilizing the feature-based decision of where to dispatch scheduled jobs, selecting from a pool of heterogeneous execution nodes.
Heavily influenced by DIRAC (http://lhcb-comp.web.cern.ch/Frameworks/DataManagement/Documents/Papers/DIRAC.pdf) but developed to distribute work based on Galaxy-like job features, designed to schedule jobs with large data input and outputs by prioritizing data locality, and allowing for segmentation of executing agents based on tags (aimed at scheduling jobs with sensitive data inputs/outputs to be executed only in some nodes)
The mode of scheduling is still a pull model (due to the nature of the execution nodes, the request needs to be started by them) but the set of features analized to start a pull can be changed. Mainly: Executors are administered by local site administrators, and they can be shared with other submission sources but Spinetta will not be used just for backfilling. The local agent can be configured to pull jobs in different cases, even when there is no free slots to run jobs:
    - Pull jobs pp to a local load of L = (100 + x)% , where x can be > 0 (for X=0 the local agents will only fetch jobs when there is unused)
    - Set a quota based on job number/rate: pull jobs up to a total rate of Y jobs per/day. This can be combined with a local load limit, or just set the X limit to `inf` and the agent will pull jobs at this rate.
    - Pull jobs based on a quota fixed on the job requirements: [total memory | total cpus | total walltime ] per day. 
Spinetta does *****NOT** perform data transfer of input/output datasets. The agents are required to fetch the dasetsats (if not cached) and push the output to the corresponding storage location. 

The server fetches the job requests and puts the job in one of the server managed queues based on:
- Sensitive data configuration: Whether the job is constrained to run in a specific job execution group (one or a set of execution nodes) that fullfil the clearence criteria.
- The source and destination object stores: Using node-reported information about proximity/latency/bandwith between execute nodes and source and/or destination object stores. 
- The execution nodes capabilities: type of dependency resolution (docker, conda, preinstalled modules), workflow based jobs (i.e CWL), and .
- Matches between job input dataset and cached datasets in worker nodes (datasets fetched by previous jobs in that node). 
- Matches between job input dataset and resolved dependencies in worker nodes (predownloaded images, prebuild conda environments).
- The reported server load: cached or real-time assesment.
- Execution nodes tags/groups listed in the job definition.

The client agent runs on each execution node (or cluster head node) and is expected to run with little effort from local site administrators, either for installation or maintenance.
- Measuring server load status.
- Measuring and reporting latency/bandwith against configured object stores.
- Pull jobs based on 
- Store configuration of local schedulers/custom runners (i.e load Galaxy-like runners configuration): can submit received jobs to a local slurm/condor/sge queuing system. These currently work only for jobs running on directories in a shared filesystems (shared between all the final execution nodes of the local cluster)
- Implement a simple local job runner: Can execute Docker based in the current node.
- Embed an executor for Galaxy-like (https://github.com/galaxyproject/galaxy) jobs such as https://github.com/ieguinoa/golang-gx-evaluation

Initial implementation: it keeps separate lists of jobs based on static/compulsory restrictions of destinations options (i.e tags and source storage).
When a request comes from an execution agent, the matchmaker iterates through the lists that fulfill the executor properties (tags and accessible storages) looking for optimal match, based on dynamic preferences defined in each allocation request (optimize for input size, estimated runtime/walltime, required memory). There is room for optimization but worst case is O(n) where n is the number of currently unscheduled jobs that match the static/enforced requirements.
The communication of polling and response messages is initially implemented using a simple native protocol over TCP (mixing custom defined msg headers + Protocol Buffers defined messages): the client sends poll requests, and agrees to execution of received jobs. The poll request (if no job is allocated) can be updated, by simple sending a new poll message with updated allocation details (e.g increased/decreased available resources in the node). The server 


TODO features:
    - Reservation requests: reserve a slot in a job execution group. The caller gets a reservation id with an expiration date that can use to submit jobs in the future.
    - Transactions like execution: sets of jobs that need to be allocated all together in the same execution node/group.
    - Accounting/billing

