# BrainObjects - Design Documentation and Ideas

One of the main motivations behind BrainObjects is the search for a new WebObjects-EOF System.



## Core Design Ideas

What does a Brain do?

 - (I) = Process sensory input
 - (S) = Store derived "insight" (knowledge)
 - (F) = Focus on important stuff … while doing multiple things at once
 - (O) = Deliver output form the processing results … e.g. as actions or answers
 - (R) = Reflect on the goals … and if the processing strategy did reach them

If a human is good at "focus" he is efficient or smart … to solve a given task.

If a human is good at "reflection" he might be considered wise … because he picks the "right" tasks to reach a goal.

Let us consider the "Brain" as the "Business Logic". We could use a framework for the above steps in a "WO-EOF" related server process too.



### BrainObjects goals

What do we want BrainObjects to provide … basically the same like

 - (I) = Input stream processing
   - e.g. common decoders
 - (S) = Storage adaptors
   - it will be tricky to deliver on common "storage access abstraction"
     - … but we could have common wrappers for "transactions" which feed performance information into the "focus" and "reflect" engine
 - (F) = Focus on important operations
   - different priorities for operations or queues or entire pipelines
     - e.g. allow pinning a pipeline to one dedicated CPU core
   - different strategies for redundancy and durability
     - e.g. persistent operations 
 - (O) = Output stream generation
   - e.g. common encoders
 - (R) = Reflect on processing strategy
   - fine grained observability to support debugging and monitoring
   - … and to allow auto-tuning of the internal "focus engine"
   
Besides the "brain" analogies listed above there are other "qualitites" to "techniques" that should be supported:

 - (L) = Object locking. In general locking is evil, but in few cases absolute necessity
 - (P) = Persistent queries, similar to the iCloudKit
   -  e.g. let a client know when the car drove more than 100,000km
 - (D) = Developer support
   - test a server without the need for a real webserver (ala. WO direct connect)
     - can provide IMG or other "static" resources
   - support rapid turnaround when it comes to handling resources
 - (A) = ACID transaction should be supported … but at the same time the server part should try to be stateless
   - … state is provided by building on top of reliable backend (storage) systems
   - if there is a need for "sessions", then they should naturally be able to life in a database
 - (Y) = Async-behavior … to keep the number of threads low and to prevent "blocking IO"
   
   
#### What BrainObjects should NOT do

Another way to define what it should do … is to figure out what it should NOT (try to) do

- Be a database
   

### BrainObjects building blocks

What are the building blocks that we might need:

#### In the Framework

 - Configuration
   - basically something like NSUserDefaults … to support deployment presents
   - maybe support for etcd or other "distributed configuration" repositories might be useful
 - Future + Promise
   - As building blocks for async interaction
     - maybe the SwiftNIO concepts could be reused
     - maybe some Distributed Objects (RPC) concepts would be helpful (like in FoundationDB)
     - more for "inter-process-large scale async" behavior … not for "intense compute"
 - Operations
   - define one (big) processing step
   - could be "made persistent" and "tracked" in a dedicated "BrainObjects store"
 - OperationQueues
   - which maintain a list of operations and schedule them based on some rules (priorities, dependencies)
   - which keep track of their performance
 - OperationPipelines
   - which could be a predefine chain of queues
 - OperationPersistencyService
   - to ensure that a "job" can not get lost and that it will 
 - TransactionUnit
   - Wraps around hand-coded interactions with (storage) backends services
   - Would allow replacing backends 
   - .. or fan out one interaction to multiple backends
   - The goals it to make a unit observable
 - BackendUnit
   - a wrapper which provides a standard way to understand the state of related backend systems 
     - … online, offline, storage limites etc
 - TuningEngine
   - an "app internal" scheduling and parameter tuning engine what will optimize the entire operation processing (pipelines)
   - … different goals might be: reduce RAM, ensure constant latency, …
 - [Tracing and Logging](Tracing_and_Logging.md)
   - .. should also include:
   - Statistics
     - to collect the performance stats for all building blocks
     - should ensure that this part does NOT become a blocking (single point of) failure
   - InstrumentationPlugins
     - to export the performance stats (traces) to different monitoring tools
       - e.g. Prometheus, Apple Instruments, OpenTelemetry …
   
   
#### Related Apps

We might need:

- A load balancing "Solution"
  - something like "WOAdaptor" + "WODaemon"?
- A Deployment Monitor
  - which could visualize the processing queues
  - which could show the cluster setup
- A Deployment (database?) Debugger
  - wo view the state of the backend systems (maybe this could be part of the normal "Dashboard"?)
- A Backend Browser
  - e.g. to display what type of API or data the BackendUnits can provide
- A Backend Data Mapper?
  - e.g. to support mapping data from some backends to some "model"-type objects
 

### What is/was WebObjects + EOF?

So what are the key building blocks that have been provided by the WebObjeczs and EOF frameworks

 - WOAdaptor + WOMonitor
   - allows load balancing .. and to "phase out" running instances .. e.g. for "smooth version migration"
   - a "GUI" (HTML) dashboard with interactive "on-off-etc" buttons?
 - WebObjects had …
   - Sessions
     - to cache data related to a specific client
   - Actions
     - to track the history of previos actions 
   - DirectActions
     - to provide "stateless" interactions
   - HTML Templates
     - to allow building responses out of predefine blocks
   - WOMonitor + Load Balancing
     - to support deployment
 - EOF had …
   - EditingContext
     - for collecting uncommited modifications
     - for providing undo-and-redo features
     - to synchronize changes with other contexts to present a consistent view (to the user)
     - for caching data from a remote source
     - to understand which storage is responsible for which data
       - so you can "commit" into multiple backends at the same time
   - Storage Adaptors
     - to convert "abstract" requests into concrete protocol commands
   - EOModeller
     - a GUI app to browse data sources
     - and to define object to storage mappings
     
     
     
     
(!) NOTE: BrainObjects is NOT trying to be a rewrite of WebObjects. But there are lesses we did learn from this "ancient" web framework.

One can and should also [learn from many of related projects…](Related_Projects.md) 