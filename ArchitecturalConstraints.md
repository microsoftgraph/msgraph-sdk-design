# Architectural Constraints

All SDKs should conform to the following constraints to ensure sufficient flexibility for developers using the SDKs in various scenarios.

- Translate Graph specific request objects into native request objects. 
- Create native client object configured to make Graph requests
- Handle native response objects with Graph optimized behavior
- Graph payloads must by serializable and deserializable
- Model coordinated graph requests as Task objects

