
# [[Reliable]] [[Scalable]] and [[Maintainable]] Applications

A data Intensive application is typically built from standard building blocks that provide commonly needed functionality

- Store data so that they or any other application can find it again [[database]]
- Remember the results of an expensive operation, to speed up reeds [[cache]]
- Search data by keywords or filter [[search index]]
- Send message to another process to be handled asynchronously [[Stream Processing]]
- Periodically crunch a large amount of accumulated data [[Batch processing]]

## [[Reliability]]

### Expectations
- Application performs the functions that the user expects
- Tolerate the mistakes made by users or user using the app in unexpected behaviour
- Performance is good enough for the required use case, under the expected data and load.
- System prevents any unauthorised access and abuse

### Types of fault/errors in system
- [[Hardware Faults]] 
	- any thing related to system failures like cpu or ram corruption
	- issue with data center
	- natural calamity 
- [[Software Errors]] 
	- Software failures can take down entire system
	- Can have legal issues if there is issue with reporting
	- Are hard to detect
- [[Human Errors]]
	- Wrong input by user are the leading cause of system failures
	- Design the system in a way that minimises opportunity of error.
	- Decouple the place where people make most mistakes from the places which can cause system failures
	- Test thoroughly at all levels, API Test, Unit Test, Integration Tests and Manual Testing and Automated Tests
	- Allow quick and easy recovery from human errors to minimise the impact in the case of failures

### Importance
it is important because of :
- Lost Productivity
- Lost revenue and reputation
- Legal issues if figure reported incorrectly
- Responsibility to our users


## [[Scalability]]
- System should withstand increasing demand
- It should have the ability to cope with increased load

### Describe load
load can be described with a few numbers which we call load parameter. Choice of params depends on the architecture of our system

- Req per sec for web server
- the ratio of reads to write in a [[database]]
- the number of simultaneously active users in the chat room
- hit rate in a [[cache]]

example:
	Twitter want to show posts to all the followers of a person, a person can have any number of followers. 
	One way to send the tweet is for a current user find all the users they follow, get their tweets merge them sort them and send them to the time line.
	This is going to be expensive because one person can follow any number
	The other approach is to make a cache of timeline of each user's home page, and whenever a tweet is posted, find that users followers and add the tweet to the followers cache like mail box.
	when they would login they would fetch all their tweets

### Describing Performance

There are two ways to describe performance:

	1. When you increase load parameter and keep the system resources same (CPU, RAM, Network bandwidth)
	2. When you increase a load parameter, how much resources need to be changed so that we can keep the performance unchnaged

For [[batch processing]] systems like [[hadoop]] we usually care about [[throughput]] which is the number of records processed/sec or total time take for a job on certain data set

In online system, important things are service response time (time taken by request response cycle)

> [!Note] Latency and response time
> Response time is what client sees, besides the actual time to process the request, it includes network delays and queueing delays
> Latency is the duration that a request is waiting to be handled (awaiting service)
> Instead of mean, it is better to use percentile take your response and sort it from fastest to slowest, then median is the halfway point.
> High percentiles of response times, also known as tail latencies, are important because they directly affect user's experience.(often user with more data will see this)

##### Percentiles in Practice

 higher percentiles becomes important in backend service that are called multiple times as part of serving a single end-user request. Even if you make the calls in parallel, the end user request still needs to wait for the slowest of the parallel calls to complete one slow request will make the entire end user experience slow.
 
- Naive approach: 
	- keep a list of response times for all request within the time window and to sort that list every minute.

- Good Approach:
	there are algorithms that can calculate a good approximation of percentiles at minimal CPU and memory cost, such as forward decay, T- digest, or HDR Histogram


### Approach for coping with Load

how do we maintain good performance even when our load parameters increased by some amount

An architecture that is appropriate for one level of load is unlikely to cope with 10 times that load,
if service is growing rapidly, we will have to rethink your architecture on every order of magnitude of load increase or perhaps even more often than that.

**Vertical scaling:** Moving to a more powerful machine
**Horizontal Scaling:** Distributing the load across multiple small machines

Good architectures usually involves a pragmatic mixture of approaches:
using several fairly powerful machines can still be simpilar and cheaper than large number of small virtual machines.

**[[Elastic Systems]]**:  they can automatically add computing resources when they detect a load increase,

**[[Manually Scaled]]**: A human analyzes the capacity and decides to add more machines to the system.

Unpredictable Load: Use elastic systems
Manually scaled systems are simpler and have fewer operational surprises.

Taking a stateful data systems from a single node to a distributed setup introduces a lot of additional complexity, so general idea is to keep your database on a single node (scale up) until scaling cost or high availability requirements forced you to make it distributed.



## [[Maintainability]]

The majority of the cost of software is not in its initial development, but in its ongoing manintenance fixing bugs, keeping its systems operational, investigating failures, adapting it to new platforms modifying it for new use case, repaying technical debts and adding new features.

To keep a software maintainable we should keep 3 points in mind.

**[[Operability]]**: Make it easy for operations teams to keep the system running smoothly.

**[[Simplicity:]]**: make it easy for new engineers to understand systems, by removing as much complexity as possible form the systems.

**[[Evolvability]]**: Make it easy for engineers to make changes to the systems in the future, adapting it for unanticipated use cases as requirements change also known as extensibility, modifiability, or plasticity.


### Operability: Making life easy for Operations

Good Operations can often work around the limitations of bad software, but good software cannot run reliably with bad operations.

A good operations team typically is responsible for the following:

- Monitoring the health of the system and quickly restoring service if it goes into a bad state.
- Tracking down the cause of problems, such as systems failures or degraded performance.
- Keeping software and platforms upto date, including  security patches.
- Keeping tabs on how different systems affect each other, so that a problematic change can be avoided before it cause damage.
- performing complex maintenance tasks, such as moving an application from one platform to another.
- Maintain security.


Good Operabilty means making routine tasks easy, allowing operations team to focus their efforts on high value activities. Data systems can do various things to make runtime tasks easy.

- Providing visibility into the runtime behaviour and internals of the system with good monitoring.
- Support for automation and integration.
- Proper documentation etc.

### Simplicity: Managing Complexity

Small software projects can have delightful, simple and expressive code, but as project get lartger, they often become very complex and difficult to understand. This slows down people working on it. Further increases the cost of maintenance.

Symptoms of Complexity:

- Explosion of the state space.
- Tight coupling of modules.
- Tangled dependencies
- Inconsistent naming and terminology
- Hacks aimed at solving performance problems
- Special casing to work around issues.

### Evolvability: Making change easy

Its extremely unlikely that your systems requirements will remain unchanged for ever. They are much more likely to be in constant flux, you learn new facts, previously unanticipated use cases emerges, business priorities changes, users request features, legal or regulatory requirements changes, growth of system forces architectural changes.

Agile working patterns provide a framework of adapting to change. TDD and refactoring are the ways to develop software in changing environments



## Summary

An application has to meet various requirements in order to be useful, there are functional requirements (what it should do, such as allowing data to be stored, retrieval, searched and processed in various ways) and some non functional requirements (general properties like security, reliability) compliance, scalability, compatibility and maintainability


**[[Reliability]]**: means making system work correctly even when faults occure. Faults can be in hardware, software, or done by humans.

**[[Scalability]]**: Strategies for keeping performance good, even when load increases.

**[[Maintainability]]**: Making life better for engineering and operations teams, who need to work with the systems, Good abstraction can help reduce complexity and makes software maintainable, Good operability means having good visibility into the systems health, and having effective ways of managing it.

