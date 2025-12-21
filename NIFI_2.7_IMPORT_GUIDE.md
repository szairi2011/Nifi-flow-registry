# NiFi 2.7 Flow Import Guide

## Overview
This directory contains a sample NiFi 2.7 JSON-based flow that can be imported into NiFi 2.7.2 and later versions.

## What's Different in NiFi 2.7?
Starting with NiFi 2.7, the flow configuration format changed from XML to JSON:
- **Before NiFi 2.7**: Flows were stored in `flow.xml.gz` format
- **NiFi 2.7+**: Flows are stored in JSON format in `flow.json.gz`

## Sample Flow Description
The `sample-nifi-2.7-flow.json` file contains a simple flow with three processors:

1. **GenerateFlowFile**: Creates sample flow files every 60 seconds
2. **LogAttribute**: Logs the attributes of incoming flow files
3. **UpdateAttribute**: Adds custom attributes (sample.attribute and environment)

The processors are connected in sequence: GenerateFlowFile → LogAttribute → UpdateAttribute

## How to Import the Flow into NiFi 2.7.2

### Method 1: Import via NiFi UI (Recommended)

1. **Start NiFi 2.7.2**
   ```bash
   cd $NIFI_HOME
   bin/nifi.sh start
   ```

2. **Access the NiFi UI**
   - Open your browser and navigate to: `http://localhost:8080/nifi`
   - (Use `https://localhost:8443/nifi` if SSL is enabled)

3. **Import the Flow**
   - Right-click on the canvas
   - Select "Upload Process Group"
   - Click "Select File" and choose `sample-nifi-2.7-flow.json`
   - Click "Upload"
   - The flow will be imported as a new process group

4. **Configure and Start the Flow**
   - Double-click the imported process group to enter it
   - Right-click on the canvas and select "Start" to start all processors
   - Or start processors individually by right-clicking each processor

### Method 2: Replace Entire Flow (Advanced)

⚠️ **Warning**: This will replace your entire NiFi configuration. Only use this on a fresh NiFi instance or if you want to completely replace your current flow.

1. **Stop NiFi**
   ```bash
   cd $NIFI_HOME
   bin/nifi.sh stop
   ```

2. **Backup your current flow** (if any)
   ```bash
   cp conf/flow.json.gz conf/flow.json.gz.backup
   ```

3. **Replace the flow**
   ```bash
   # Compress the sample flow
   gzip -c sample-nifi-2.7-flow.json > conf/flow.json.gz
   ```

4. **Start NiFi**
   ```bash
   bin/nifi.sh start
   ```

### Method 3: Using NiFi Registry (For Version Control)

1. **Set up NiFi Registry** (if not already running)
   ```bash
   cd $NIFI_REGISTRY_HOME
   bin/nifi-registry.sh start
   ```

2. **Connect NiFi to Registry**
   - In NiFi UI, click the menu (≡) in the top right
   - Select "Controller Settings"
   - Go to "Registry Clients" tab
   - Add a new registry client pointing to your NiFi Registry instance

3. **Import and Version the Flow**
   - Import the flow using Method 1
   - Right-click the process group
   - Select "Version" → "Start version control"
   - Save the flow to the registry
   - Now you can track changes and share flows across NiFi instances

## Scheduling the Flow

Once imported, you can schedule the flow:

1. **Individual Processor Scheduling**
   - Right-click on any processor
   - Select "Configure"
   - Go to "Scheduling" tab
   - Adjust settings:
     - Scheduling Strategy: TIMER_DRIVEN, CRON_DRIVEN, or EVENT_DRIVEN
     - Run Schedule: Set the frequency (e.g., "60 sec", "0 0 12 * * ?")
     - Concurrent Tasks: Number of concurrent threads

2. **Process Group Level Scheduling**
   - Right-click the process group
   - Select "Start" to start all processors
   - Select "Stop" to stop all processors

## Flow Structure

The JSON flow structure includes:

```json
{
  "flowContents": {
    "processors": [...],      // Processor definitions
    "connections": [...],     // Connections between components
    "labels": [...],          // Visual labels
    "controllerServices": [], // Shared services
    ...
  },
  "parameterContexts": {},    // Parameter contexts for externalized properties
  "flowEncodingVersion": "1.0"
}
```

## Customization

You can customize the flow by:

1. **Modifying processor properties** in the `properties` section of each processor
2. **Adding new processors** to the `processors` array
3. **Creating connections** between processors in the `connections` array
4. **Adding parameter contexts** for externalized configuration
5. **Adding controller services** for shared functionality (e.g., database connections)

## Troubleshooting

### Flow doesn't import
- Ensure you're using NiFi 2.7.0 or later
- Validate the JSON syntax using a JSON validator
- Check NiFi logs: `$NIFI_HOME/logs/nifi-app.log`

### Processors show validation errors
- Some processors may require additional configuration
- Check that all required properties are set
- Ensure controller services (if any) are enabled

### Flow doesn't appear after Method 2
- Check that the file is properly gzipped
- Ensure the file is named exactly `flow.json.gz`
- Review NiFi logs for errors during startup

## Additional Resources

- [Apache NiFi Documentation](https://nifi.apache.org/docs.html)
- [NiFi 2.7 Release Notes](https://nifi.apache.org/release-notes.html)
- [NiFi User Guide](https://nifi.apache.org/docs/nifi-docs/html/user-guide.html)
- [Flow Design Best Practices](https://nifi.apache.org/docs/nifi-docs/html/user-guide.html#flow-design)

## Version Information

- **Flow Format Version**: 1.0
- **Compatible with**: Apache NiFi 2.7.0 and later
- **Tested on**: Apache NiFi 2.7.2
- **Flow Encoding**: JSON (uncompressed)

## Contributing

To add more sample flows to this registry:

1. Design your flow in NiFi 2.7+
2. Export it using the "Download" option in the UI
3. Add it to this repository with descriptive documentation
4. Update this README with a reference to your flow

## License

This sample flow is provided as-is for demonstration and educational purposes.
