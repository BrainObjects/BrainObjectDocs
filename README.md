# BrainObjects - Design Documentation and Ideas

One of the main motivations behind BrainObjects is the search for a new WebObjects-EOF System.

The BrainObjects project is located at: <https://www.github.com/BrainObjects/BrainObjectDocs>


## Core Design Ideas

What does a Brain do?

 - (I) = Process sensory input
 - (S) = Store derived "insight" (knowledge)
 - (F) = Focus on important stuff … while doing multiple things at once
 - (O) = Deliver output from the processed results … e.g. as actions or answers
 - (R) = Reflect on the goals … and check if the processing strategy did reach them

If a human is good at "focus" the human is considered efficient or smart … to solve a given task.

If a human is good at "reflection" the human might be considered wise … because the human picks the "right" tasks to reach a goal.

Let us consider the "Brain" as the "Business Logic". 

We could use a framework for the above steps in a "WO-EOF" related server process too.



### BrainObjects goals

What do we want BrainObjects to provide:

 - (I) = Input stream processing
   - e.g. common decoders (HTTP, JSON, XML, etc.)
 - (S) = Storage adaptors
   - it will be tricky to deliver one common "storage access abstraction"
     - … but we could have common wrappers for "transactions" which feed performance information into the "focus" and "reflect" engine
 - (F) = Focus on important operations
   - different priorities for operations, or queues, or entire pipelines
     - (e.g. allow pinning a pipeline to one dedicated CPU core)
   - different strategies for redundancy and durability
     - (e.g. persistent operations which can be recovered after a process "crash")
 - (O) = Output stream generation
   - e.g. common encoders (HTTP, HTML, JSON, XML, etc.)
 - (R) = Reflect on the processing strategy
   - fine grained observability (traceability) to support debugging and deployment monitoring
   - … and to allow auto-tuning of the internal "focus engine" (operation scheduling)
   
Besides the "brain" analogies listed above there are other characteristics of "techniques" that should be supported:

 - (L) = Object locking. In general locking is evil, but in few cases an absolute necessity
 - (P) = Persistent and long running queries, similar to the iCloudKit
   -  e.g. let a client know when the car drove more than 100,000km
 - (D) = Developer support
   - test a server without the need for a real web-server (like WO direct connect)
     - can provide images (e.g. JPEG) or other "static" resources too
   - support rapid turnaround when it comes to handling resources
 - (A) = ACID transactions should be supported … but at the same time the server part should try to be stateless
   - … state is provided by building on top of reliable backend storage systems
   - if there is a need for "sessions", then they should naturally be able to be persistent too
 - (Y) = Async-behaviour … to keep the number of threads low and to prevent "blocking IO"
   - e.g. for large building blocks via operation pipelines with clearly defined handover conventions (IO queues)
   - e.g. for small building blocks async-wait might be used as well
   
   
   
#### What BrainObjects should NOT do

Another way to define what it should do … is to figure out what it should NOT (try to) do

- Try NOT to be a data store
- Try NOT to be a rewrite of WebObjects (1)
- Try NOT to be a rewrite of Vapour
    
     
(1) NOTE: But there are important lessons we did learn from this "ancient" WebObjects framework.



## Continue reading ...

… about ideas for [technical building blocks.](00-Building_Blocks.md)  