# BrainObjects - Technical building blocks

What are the actual building blocks that we might need?


## In the Framework

 - `Configuration`
   - basically something like `NSUserDefaults` … to support deployment presents
   - maybe support for `etcd` or other "distributed configuration" repositories might be useful
   - can read JSON etc
 - Future + Promise
   - As building blocks for async interaction
     - maybe the SwiftNIO concepts could be reused
     - maybe some Distributed Objects (RPC) concepts would be helpful (like in FoundationDB)
     - more for "inter-process-large scale async" behaviour … not for "intense compute"
     - 2022 … now basically obsolted due to … `async-await + (distributed) Actors`
 - `JobContext`
	 - the "box" which contains/collects the state of one specific Job
	 - it gets passed from one Operation to the next
 - `JobController` ? vs `PipelineController` ?
	 - keeps track of JobContexts
	 - schedules Operations … according to JobContext priorities
  - `WorkflowDefinition`
	- is an "immutable" description of a job `ProcessingPipeline`
	- can be defined in the `Configuration`
- `ProcessingPipeline`
	 - would be a predefine chain of "living" (preallocated an reuseable) `Operation`s
- `Operation`
	- define one (big) processing step
	- could be "made persistent" and "tracked" in a dedicated "BrainObjects store"
	- how are multiple operations "chained" to the next one?
	- have input and output ports … which connect (chain) different operations
	- will "block" if output can not deliver JobContext to next input queue (because it is full)
		- which will document / build up back pressure
 - `OperationQueue` ??
   - which maintain a list of operations and schedule them based on some rules (priorities, dependencies)
   - which keep track of their performance
 - OperationPersistencyService
   - to ensure that a "job" can not get lost and that it will 
- `SwiftNIORequestDispatcher`
	- map "HTTP routes" to the root `Operation` of known `ProcessingPipeline`
	- which are are identified by a `WorkflowDefinition`
 - TransactionUnit
   - Wraps around hand-coded interactions with (storage) backends services
   - Would allow replacing backends 
   - .. or fan out one interaction to multiple backends
   - The goals it to make a unit observable
 - BackendUnit
   - a wrapper which provides a standard way to understand the state of related backend systems 
     - … online, offline, storage limites etc
 - TuningEngine
   - an "app internal" scheduling and parameter tuning engine what will optimise the entire operation processing (pipelines)
   - … different goals might be: reduce RAM, ensure constant latency, …
 - [Tracing and Logging](Tracing_and_Logging.md)
   - .. should also include:
   - Statistics
     - to collect the performance stats for all building blocks
     - should ensure that this part does NOT become a blocking (single point of) failure
   - InstrumentationPlugins
     - to export the performance stats (traces) to different monitoring tools
       - e.g. Prometheus, Apple Instruments, OpenTelemetry …
   
   
## Related Apps

We might need:

- A load balancing "Solution"
  - something like "WOAdaptor" + "WODaemon"?
- A Deployment Monitor (Dashboard)
  - which could visualize the processing queues
  - which could show the cluster setup … include "external" services (databases etc.)
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
     - to track the history of previous actions 
   - DirectActions
     - to provide "stateless" interactions
   - HTML Templates
     - to allow building responses out of predefine blocks
   - WOMonitor + Load Balancing
     - to support deployment
 - EOF had …
   - EditingContext
     - for collecting uncommitted modifications
     - for providing undo-and-redo features
     - to synchronise changes with other contexts to present a consistent view (to the user)
     - for caching data from a remote source
     - to understand which storage is responsible for which data
       - so you can "commit" into multiple backends at the same time
   - Storage Adaptors
     - to convert "abstract" requests into concrete protocol commands
   - EOModeller
     - a GUI app to browse data sources
     - and to define object to storage mappings
     
(!) NOTE: BrainObjects is NOT trying to be a rewrite of WebObjects. But there are lesses we did learn from this "ancient" web framework.


## Continue reading ...

One can and should also [learn from many of related projects.](10-Related_Projects.md) 