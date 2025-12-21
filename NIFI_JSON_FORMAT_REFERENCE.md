# NiFi 2.7 JSON Flow Format - Quick Reference

## Flow Structure Overview

The NiFi 2.7 JSON flow format has the following top-level structure:

```json
{
  "flowContents": { ... },           // Main flow definition
  "externalControllerServices": {}, // External controller services
  "parameterContexts": {},           // Parameter contexts for variables
  "flowEncodingVersion": "1.0",     // Format version
  "parameterProviders": {}           // Parameter providers
}
```

## Key Components

### 1. Process Group (flowContents)
The root object containing all flow components:

- `identifier`: Unique ID for the process group
- `name`: Display name
- `position`: {x, y} coordinates on canvas
- `processors`: Array of processor definitions
- `connections`: Array of connections between components
- `labels`: Visual labels for documentation
- `controllerServices`: Shared services
- `processGroups`: Nested process groups
- `inputPorts`: Input ports for the group
- `outputPorts`: Output ports for the group
- `funnels`: Funnel components

### 2. Processor Definition
Each processor includes:

```json
{
  "identifier": "unique-id",
  "name": "Processor Name",
  "type": "org.apache.nifi.processors.package.ProcessorClass",
  "bundle": {
    "group": "org.apache.nifi",
    "artifact": "nifi-nar-name",
    "version": "2.7.2"
  },
  "properties": {
    "Property Name": "value"
  },
  "schedulingPeriod": "60 sec",
  "schedulingStrategy": "TIMER_DRIVEN",
  "autoTerminatedRelationships": ["failure"]
}
```

### 3. Connection Definition
Connections link components together:

```json
{
  "identifier": "connection-id",
  "source": {
    "id": "source-processor-id",
    "type": "PROCESSOR",
    "groupId": "parent-group-id"
  },
  "destination": {
    "id": "destination-processor-id",
    "type": "PROCESSOR",
    "groupId": "parent-group-id"
  },
  "selectedRelationships": ["success"],
  "backPressureObjectThreshold": 10000,
  "backPressureDataSizeThreshold": "1 GB"
}
```

## Common Processor Types

### Standard Processors
- `org.apache.nifi.processors.standard.GenerateFlowFile` - Generate test data
- `org.apache.nifi.processors.standard.LogAttribute` - Log attributes
- `org.apache.nifi.processors.standard.GetFile` - Read files from disk
- `org.apache.nifi.processors.standard.PutFile` - Write files to disk
- `org.apache.nifi.processors.standard.RouteOnAttribute` - Route based on attributes

### Attribute Processors
- `org.apache.nifi.processors.attributes.UpdateAttribute` - Add/modify attributes

### HTTP Processors
- `org.apache.nifi.processors.standard.InvokeHTTP` - Make HTTP requests
- `org.apache.nifi.processors.standard.HandleHttpRequest` - Handle incoming HTTP
- `org.apache.nifi.processors.standard.HandleHttpResponse` - Send HTTP responses

### Database Processors
- `org.apache.nifi.processors.standard.ExecuteSQL` - Execute SQL queries
- `org.apache.nifi.processors.standard.PutSQL` - Insert/update database records

## Scheduling Strategies

- `TIMER_DRIVEN` - Run on a fixed schedule (e.g., "60 sec", "5 min")
- `CRON_DRIVEN` - Run on cron schedule (e.g., "0 0 12 * * ?")
- `EVENT_DRIVEN` - Run when data is available

## Bundle Artifacts (NARs)

Common NiFi Archives (NARs):
- `nifi-standard-nar` - Standard processors
- `nifi-update-attribute-nar` - UpdateAttribute processor
- `nifi-kafka-nar` - Kafka processors
- `nifi-aws-nar` - AWS processors
- `nifi-azure-nar` - Azure processors
- `nifi-gcp-nar` - Google Cloud processors

## Relationship Types

Common processor relationships:
- `success` - FlowFile processed successfully
- `failure` - Processing failed
- `original` - Original FlowFile (before split/merge)
- `matched` - Matched a condition
- `unmatched` - Did not match a condition

## Property Value Types

Properties can be:
- **Literal values**: `"1KB"`, `"true"`, `"info"`
- **Expression Language**: `"${filename}"`, `"${now():format('yyyy-MM-dd')}"`
- **Controller Service references**: Links to shared services
- **File paths**: `"/path/to/file"`
- **Resource URLs**: `"http://example.com/resource"`

## Parameter Contexts

For externalized configuration:

```json
"parameterContexts": {
  "context-id": {
    "identifier": "context-id",
    "name": "My Parameters",
    "parameters": [
      {
        "name": "database.url",
        "value": "jdbc:mysql://localhost:3306/mydb",
        "sensitive": false
      }
    ]
  }
}
```

Use in properties: `"#{database.url}"`

## Controller Services

For shared functionality:

```json
{
  "identifier": "service-id",
  "name": "DBCPConnectionPool",
  "type": "org.apache.nifi.dbcp.DBCPConnectionPool",
  "properties": {
    "Database Connection URL": "jdbc:mysql://localhost:3306/mydb",
    "Database Driver Class Name": "com.mysql.jdbc.Driver"
  }
}
```

## Best Practices

1. **Use meaningful names** for processors and connections
2. **Add comments** to processors to document their purpose
3. **Use labels** to organize and document your flow
4. **Enable back pressure** to prevent overwhelming downstream processors
5. **Set appropriate timeouts** for flowfile expiration
6. **Auto-terminate unused relationships** to prevent data from accumulating
7. **Use parameter contexts** for environment-specific values
8. **Version your flows** using NiFi Registry

## Converting from XML to JSON

If you have old XML flows:
1. Import them into NiFi 2.7+
2. NiFi will automatically convert them to JSON format
3. Export from NiFi 2.7+ to get JSON format

## Validation

To validate your JSON flow:
```bash
# Basic JSON syntax validation
python3 -m json.tool flow.json > /dev/null

# Or using jq
jq empty flow.json
```

## Example: Minimal Flow

```json
{
  "flowContents": {
    "identifier": "root",
    "name": "Root",
    "processors": [
      {
        "identifier": "proc-1",
        "name": "Generate",
        "type": "org.apache.nifi.processors.standard.GenerateFlowFile",
        "bundle": {
          "group": "org.apache.nifi",
          "artifact": "nifi-standard-nar",
          "version": "2.7.2"
        },
        "properties": {},
        "autoTerminatedRelationships": ["success"]
      }
    ],
    "connections": []
  },
  "flowEncodingVersion": "1.0"
}
```

## Resources

- [NiFi Developer Guide](https://nifi.apache.org/developer-guide.html)
- [Expression Language Guide](https://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html)
- [Processor Documentation](https://nifi.apache.org/docs/nifi-docs/components/)
