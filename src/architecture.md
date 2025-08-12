# Architecture

## Definitions
Generally, if we separate the building blocks of stream processing engines into their subsequent parts, you could likely end up with something
that resembles:

- The storage engine
- The storage implementation
- The computation engine.

where

The "storage engine" resembles the logic that determines how data flowing into the system is arranged, partitioned and indexed. This logic may also
include how data is read from the storage implementation such as caching strategies, how parallel consumers are given separate indexes etc.

The "storage implementation" is the hardware and software pairing that makes up the layer at which data is actually put when written. This is akin to RAM on an in-memory implementation or a object storage implementation within cloud service providers. This layer has no logic except for standard get/put operations that allow you to read and write data given a specific set of parameters.

The "computation engine" would be all the frameworks, libraries and implementations that surround reading and writing data from/to storage engines and applying arbitrary logic to these data streams as they are read/written. Oftentimes, this would be done with a client library and some services before distributed stream processing became vogue.

## Rationale

As the popularity of stream processing has increased over time, there are a certain amount of problems that have arisen where certain implementations may solve a subset of problems the implementors are trying to solve, but not all. These problems include:

- Cost-effectiveness
- Fault-tolerance
- Underutilization of resources
- ease of operation
- ease of deployment

The lack of flexibility when it comes to these areas of improvement have generally been attributed to the coupling or non-coupling of the above parts into a single program, or separated into separate processes. For instance, the storage engine and storage layer were initially coupled together in Apache Kafka, which has a performance benefit at the time based on how SSD's tend to write contiguous arrays of data. As the storage engine generally requires less node-specific resources than the storage implementation, deployments tend to over-provision nodes as the flow of data increases. The coupling of the storage engine has also lead to the inability for these implementations to use more cost-effective solutions such as object storage.

Higgins takes a different approach, namely:

- Separating the storage implementation out and having a generalized approach to it, including a pointer system and dereferencing mechanism.
- Adding in a computation framework for stream processing, including the high-level operators of streams and an embedded FFI for lower-level operations.

These design choices attempt to solve the following problems:

- Have strict rules around immutability and indexing, ascerting guarantees such as "at least once" processing for all stream computations.
- Allowing the use of different storage backends with different throughput and fault-tolerance guarantees.
- Reducing the overhead of potentially managing multiple separate deployments for a storage engine and a stream computation engine.
