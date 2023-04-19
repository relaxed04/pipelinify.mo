# pipelinify.mo
Move data chunks between canisters

## Overview
The Pipelinify module is an implementation of the framework/template-method pattern - it allows you to "hook in" to different parts of a data transformation pipeline.  
Possible hooks include: `onDataWillBeLoaded`, `onDataReady`, `onPreProcess`, `onProcess`, `onPostProcess`, `onDataWillBeReturned`.  
In addition to specifying such a hook based interface, Pipelinify expects and ensures data transmitted between containers to be stable.  
When stable data is received, it destabilizes the data internally before making it available to hooks for modification.  
Because of the flexibility of the hook interface, Pipeliniy has many use cases:  
The recieving canister, where data is pushed to, could be used as a utility canister to transform data in different ways - as is the case in the test examples. Alternatively, the receiving canister could mainly leverage Pipelinifies stable data transmission and act as the next step in a chain of canister calls. 
Splitting up your application into seperate domains ran on different cannisters could be beneficial when upgrades take place, Pipelinify allows you to accomplish this architecture pattern.

We will refer to the canister making a call to the Pipelinify canister as the consumer canister.
We will refer to the canister which implements the Pipelinify hooks as the producer canister.

### Data Inputs

#dataIncluded
- The consumer canister can send data directly to the producer canister, none of the data is stored on the producer

#pull
- the consumer canister can ask Pipelinify to pull the necessary data from another source
  - this source must be an actor which implements specific data access functions

#local
- your producer canister can be set up to contain the necessary data in memory

#push
  - your consumer can push data chunks to be stored on the producer


### Execution

#onLoad
- this option calls all processing hook functions: onPreProcess, onProcess, onPostProcess

#manual
- this option skips the onPostProcess hook and returns the data if data was processed.


### Output

#push 
- pipelinify converts the data back to stable data and returns the response to the calling consumer

#pull
- returns the ID of the canister so that the consumer can make a subsequent request for the data stored in the producers memory

#local
- the producer saves the output data in local memory and waits for the consumer to request it


## References
- for further reading on stable data and persistence check out:
https://medium.com/dfinity/ic-internals-orthogonal-persistence-9e0c094aac1a
- for stable data structure implementations and transformations check out:
https://github.com/icdevs/candy_library