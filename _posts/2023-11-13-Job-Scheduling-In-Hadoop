---
title: Run-Time Priority Algorithm - Job Scheduling in Hadoop Clusters
date: 2023-11-13 12:00:00 -5000
categories: [BigData, Hadoop, Scheduling]
tags: [bigdata, mapreduce, hadoop, jobs, scheduling]     # TAG names should always be lowercase
---

Before moving to the technical details of scheduling, firstly we need to be familiar with some terms of physical locations and logical units which we will be using more often in this blog...

1. **Cluster :** A cluster represents the entire hadoop environment which includes the server, nodes, racks, data-centers the collectively makeup a distributed file system.
2. **Server :** A server refers to an individual machine or a computer within the cluster. servers can host multiple nodes.
3. **Node :** A node is a server that is part of the Hadoop cluster and can run various Hadoop services and tasks. A node is the basic computational unit in the cluster and hosts Task Trackers (in the case of MapReduce) or DataNodes (in the case of HDFS).
4. **Rack :** A rack is a collection of nodes that are physically grouped together, typically within the same network switch or a network proximity that allows for high-speed data transfer.
5. **Data center :** Data centers are the highest-level hierarchy, comprising multiple racks and, in some cases, multiple clusters. They are geographically separated and may contain multiple clusters or racks. Data centers are used for redundancy and fault tolerance, ensuring that data is replicated across different locations.

## How Jobs and Task Tracking Works:
1. **Job Tracker :** In Hadoop's map-reduce framework, the JobTracker is the master component which manages the execution of the of Jobs (Data processing tasks). It keeps track of job status, monitors task execution, and schedule tasks on available task trackers.
2. **Task Tracker :** The task trackers are worker nodes within the hadoop cluster. they are responsible for executing individual tasks which can be either map tasks or reduce tasks as a part of a MapReduce job.

So, when there is a job submitted for processing, the Job Tracker's primary responsibility is to schedule and monitor tasks on available task trackers. The rules for Task trackers are:
- **Local Task Assignment:** The job Tracker first checks whether there are any available Task Trackers on the same server(node) where the data to be process is located. This is known as `locality optimization`. If it finds a free slot on a Task Tracker on the same server, it assigns the task to that Task Tracker. This is preferred because it minimizes data transfer over the network and leverages data locality, which can significanty improve performances.
- **Rack Awareness:** If the Job Tracker cannot find a free slot on a Task tracker within the same server, it checks for Trask Trackers on the same rack (a collection of interconnected servers). Hadoop is desinged to be `rack-aware`, meaning it understands the network topology of the cluster. This reduces network usage compared to searching for Task Trackers on distant racks.
- **Any Available Task Trackers:** If it cannot find a suitable Task Tracker within the same server or the same rack, the Job Tracker looks for any avaiblable Task Tracker in the cluster to execute the task. While this may result in more data transfer over the network, it ensures the task. While this may result in more data transfer over the network, it ensures that the taks gets executed even if there are no local or rack-local options available.

The Job Tracker tries to optimize task assignment by starting with the local Task Trackers on the same server, then expanding to the same rack, and finally considering any available Task Tracker in the cluster. This approach is designed to minimize network data transfer and make the best use of data locality, which is critical for efficient processing in Hadoop's distributed environment.

Certainly! Let's continue from where the article left off.

## Algorithm:
```python
for job in sorted_jobs:
    if buffer_queue_size(node) < job_size(node):
        assign_to_fixed_server(job)
        wait_until_process_complete()
    elif buffer_queue_size(node) > job_size(node):
        check_for_floating_servers(job)
    else:
        if threshold_condition():
            assign_to_free_slots(job)

            for _ in range(deQueueRate):
                if buffer_queue_size() < 1:
                    dequeue()
                    break
```
### Explanation:

**Job Iteration:**

The algorithm iterates over the jobs in sorted_jobs.

**Fixed Server Assignment:**

If the buffer queue size on the node (buffer_queue_size(node)) is less than the job size on that node (job_size(node)), the job is assigned to a fixed server using the assign_to_fixed_server function.
The algorithm then waits until the assigned job is completed (wait_until_process_complete).

**Floating Server Check:**

If the buffer queue size on the node is greater than the job size, the algorithm checks for the availability of floating servers using the check_for_floating_servers function.

**Threshold Check and Free Slot Assignment:**

If neither of the above conditions is met, the algorithm checks for a threshold condition using threshold_condition().
If the threshold condition is met, the job is assigned to free slots on the node using the assign_to_free_slots function.

**Dequeueing Loop:**

After assigning the job to free slots, the algorithm enters a loop (for _ in range(deQueueRate)) to dequeue jobs from the buffer queue based on a specified deQueueRate.
If the buffer queue size drops below 1 during the loop, a job is dequeued using the dequeue function, and the loop breaks.


## Node Capacities and Job Progress Shares

Now, let's introduce the concept of node capacities and job progress shares to further enhance our understanding of the intricacies of job scheduling in Hadoop clusters.

### Node Capacities

In addition to the hierarchical structure of the Hadoop cluster, it's essential to consider the capacities of individual nodes. Each node in the cluster has a certain number of available slots for task execution. For instance:

- Node A: 2 slots
- Node B: 2 slots
- Node C: 2 slots

These capacities dictate how many tasks a node can concurrently handle, providing a basis for optimizing the distribution of workload.

### Job Progress Shares

Jobs within the Hadoop environment are characterized by progress shares, indicating the percentage of completion. This metric becomes crucial when prioritizing job allocation within the cluster. Let's review the progress shares for our example jobs:

- Job 1: 25%
- Job 2: 10%
- Job 3: 15%
- Job 4: 5%
- Job 5: 20%
- Job 6: 8%
- Job 7: 12%
- Job 8: 18%
- Job 9: 3%
- Job 10: 22%

Understanding these progress shares is fundamental to the Toilet Queue Model Algorithm, which we will explore shortly.

## Run-Time Priority Algorithm

### Sorting Jobs by Progress Share

Before delving into the algorithm, let's sort our jobs in ascending order of progress share:

1. Job 9 (Progress Share: 3%)
2. Job 4 (Progress Share: 5%)
3. Job 6 (Progress Share: 8%)
4. Job 3 (Progress Share: 15%)
5. Job 7 (Progress Share: 12%)
6. Job 2 (Progress Share: 10%)
7. Job 8 (Progress Share: 18%)
8. Job 5 (Progress Share: 20%)
9. Job 10 (Progress Share: 22%)
10. Job 1 (Progress Share: 25%)

This sorted order will guide the allocation process based on progress shares.

### Job Allocation Loop

Now, let's walk through the allocation loop, considering the node capacities and progress shares:

#### a. Job 9 (Progress Share: 3%)

    Node A (2 slots) can accommodate this job.

    Assign Job 9 to Node A (remaining slots on Node A: 1).

    Decrement the number of jobs in the queue.

#### b. Job 4 (Progress Share: 5%)

    Node A (remaining slots: 1) can still accommodate this job.

    Assign Job 4 to Node A (remaining slots on Node A: 0).

    Decrement the number of jobs in the queue.

#### c. Job 6 (Progress Share: 8%)

    Node B (2 slots) can accommodate this job.

    Assign Job 6 to Node B (remaining slots on Node B: 1).

    Decrement the number of jobs in the queue.

#### d. Job 3 (Progress Share: 15%)

    Node B (remaining slots: 1) can still accommodate this job.

    Assign Job 3 to Node B (remaining slots on Node B: 0).

    Decrement the number of jobs in the queue.

#### e. Job 7 (Progress Share: 12%)

    Node C (2 slots) can accommodate this job.

    Assign Job 7 to Node C (remaining slots on Node C: 1).

    Decrement the number of jobs in the queue.

#### f. Job 2 (Progress Share: 10%)

    Node C (remaining slots: 1) can still accommodate this job.

    Assign Job 2 to Node C (remaining slots on Node C: 0).

    Decrement the number of jobs in the queue.

#### g. Job 8 (Progress Share: 18%)

    No nodes have available slots, but there are floating servers.

    Assign Job 8 to a floating server.

    Decrement the number of jobs in the queue.

#### h. Job 5 (Progress Share: 20%)

    No nodes or floating servers available, so it's waiting for a slot.

    Decrement the number of jobs in the queue.

#### i. Job 10 (Progress Share: 22%)

    Job 10 has the highest progress share, but no nodes or floating servers are available, so it's waiting for a slot.

    Decrement the number of jobs in the queue.

#### j. Job 1 (Progress Share: 25%)

    Job 1 has the highest progress share, but no nodes or floating servers are available, so it's waiting for a slot.

    Decrement the number of jobs in the queue.

    The jobs that can be immediately allocated have been assigned. Jobs 5, 10, and 1, with higher progress shares, are waiting for slots to become available on the nodes. Job 8 is running on a floating server. The algorithm waits for slots to open as jobs complete on the nodes.

### Handling Floating Servers and Waiting Jobs

When no nodes have available slots, the algorithm assigns jobs to floating servers. Jobs with higher progress shares, such as Job 8 (Progress Share: 18%), are allocated to floating servers. However, jobs like Job 5 (Progress Share: 20%) and Job 10 (Progress Share: 22%) with no available nodes or floating servers must wait for slots to become available.

### Managing the Queue

As jobs complete on nodes, slots become available, and the algorithm continuously checks the queue for waiting jobs. Jobs with higher progress shares take priority, ensuring the most efficient use of resources.


## What IF's
1. What if a job has a very high progress share but there are no available slots on any nodes or floating servers?

        Example: Suppose Job X has a progress share of 90%, indicating it's near completion. However, all nodes and floating servers are occupied. In this case, Job X will be queued until a slot becomes available.

2. What if a floating server running a high-progress job becomes unavailable or fails during execution?

        Example: If a floating server running Job Y fails, the algorithm should detect the failure and reassign Job Y to another available resource or reschedule it for execution on a different node.

3. What if the cluster experiences a sudden increase in job submissions?

        Example: If the cluster suddenly receives 20 new jobs, the algorithm should dynamically allocate resources, perhaps by adjusting priorities or optimizing resource utilization, to handle the increased workload efficiently.

4. What if a node's capacity changes dynamically during job execution (e.g., due to hardware upgrades or failures)?

        Example: If Node Z undergoes a hardware upgrade and now has additional slots, the algorithm should dynamically adapt and consider Node Z's increased capacity in future job allocations.

5. What if a job with a lower progress share is allocated to a node with a higher capacity, while a higher progress job is waiting for a slot on a node with a lower capacity?

        Example: If Job P (progress share 15%) is allocated to Node A (capacity 4 slots), while Job Q (progress share 10%) is waiting for a slot on Node B (capacity 2 slots), the algorithm should consider reallocating Job Q to Node A for better resource utilization.

6. What if the network conditions in the cluster change, impacting data transfer times between nodes?

        Example: If network congestion increases, the algorithm might adjust its strategy to favor local task assignment or implement network-aware scheduling to minimize the impact on job execution time.

7. What if a job's progress share changes dynamically during execution (e.g., due to data dependencies or processing complexities)?

        Example: If Job R's progress share increases from 20% to 30% due to unforeseen complexities, the algorithm should dynamically adjust its priority, potentially moving Job R up in the queue for faster execution.

8. What if a new node is added to the cluster or an existing node is decommissioned?

        Example: If a new Node C is added to the cluster, the algorithm should recognize the change in capacity and adjust its scheduling decisions accordingly to utilize the additional resources.

9. What if there are conflicting resource requirements among jobs, such as memory-intensive jobs competing for the same nodes?

        Example: If Job S and Job T both require significant memory and are vying for the same nodes, the algorithm might consider memory constraints in its decision-making process, preventing resource contention.

10. What if the algorithm encounters a scenario where certain jobs consistently wait for a long time while others get immediate allocation?

        Example: If Job U consistently waits longer than other jobs, the algorithm should be monitored and tuned to identify potential biases or inefficiencies. Adjustments may include refining priority calculations or revisiting resource allocation strategies.

11. What if a job has external dependencies, and those dependencies are not available when the job is scheduled to run?
        
        Example: Job V requires data from an external source, but the data is not available when the job is scheduled. The algorithm may need to implement mechanisms to handle such dependencies, potentially rescheduling the job when the required data becomes available.

12. What if a job has resource requirements that cannot be met by any single node in the cluster?
        
        Example: Job W requires more slots than any single node can provide. The algorithm should be able to handle multi-node job assignments or implement a strategy to split the job into smaller tasks that can run concurrently on multiple nodes.

13. What if a high-priority job has been waiting for a long time, and no nodes or floating servers become available?
        
        Example: Job X, with a high progress share, has been waiting for execution, but no resources are available. The algorithm may need to periodically reassess the queue and prioritize jobs that have been waiting for an extended period.

14. What if there are sudden fluctuations in the available network bandwidth between nodes?
        
        Example: If the network bandwidth between nodes decreases due to external factors, the algorithm might need to adjust its task assignment strategy to minimize the impact on data transfer times.

15. What if certain nodes consistently experience hardware failures or degraded performance?
        
        Example: Nodes in Rack A are prone to hardware failures. The algorithm should be resilient to such situations, considering the historical reliability of nodes when making scheduling decisions.

16. What if there are specific jobs that require execution within strict time constraints (real-time processing)?
        
        Example: Job Y requires real-time processing, and delays could have significant consequences. The algorithm may need to incorporate real-time job prioritization to ensure timely execution.

17. What if there is a need for job prioritization based on business priorities or service-level agreements (SLAs)?
        
        Example: Jobs from the finance department may have higher business priorities than jobs from other departments. The algorithm might need to incorporate business-driven priorities or SLAs in its decision-making process.

18. What if a node becomes temporarily unavailable due to maintenance or other planned activities?
        
        Example: Node Z is undergoing scheduled maintenance. The algorithm should be aware of such planned downtimes and reschedule tasks to avoid disruptions to job execution.

19. What if there are security or compliance requirements that impact task assignment (e.g., data residency regulations)?
        
        Example: Certain jobs may need to adhere to data residency regulations, requiring them to be processed in specific geographic locations. The algorithm should consider these requirements when making scheduling decisions.

20. What if there is a need to provide fairness in resource allocation among different users or departments sharing the Hadoop cluster?
        
        Example: To ensure fairness, the algorithm may need to implement policies that distribute resources equitably among different users or departments, preventing one user from monopolizing cluster resources.

## Conclusion

The Run-Time Priority Algorithm exemplifies a sophisticated approach to job scheduling in Hadoop clusters. By integrating node capacities and job progress shares, this algorithm optimizes resource utilization and ensures the timely execution of data processing tasks. As we navigate the complexities of big data processing, understanding advanced scheduling algorithms becomes paramount for harnessing the full potential of Hadoop clusters.


