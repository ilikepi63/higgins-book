# Configuration Files

Configurations are *usually* small toml formatted files that define how a subsection of your higgins implementation should work, or the streams and schema that should be present in your instance of higgins.

For instance, a basic configuration that sets up a simple stream looks something along the lines of:

```toml
[schema.customer]
id = "string"
first_name = "string"
last_name = "string"
age = "int32"

[streams.customer]
partition_key = "id"
schema = "customer"
```

Here, we can see that we initially define a "customer" schema, then create a stream with that schema attached to it.

To upload your configuration, simply invoke the `upload_configuration` with the byte representation of the above config:

```rust
let config = std::fs::read_to_string("{file_name}").unwrap();
client.upload_configuration(config.as_bytes()).unwrap();
```

Higgins will apply the above config and you'd be able to take/produce to the given stream. A collection of configurations are referred to as a "Typography" and the set of configurations that have been applied to your instance are considered the typography of your instance.
