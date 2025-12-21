# Nifi-flow-registry
Created to version control Nifi flows --  A Nifi Flow registry basically

## Contents

### NiFi 2.7+ (JSON Format)
- **[sample-nifi-2.7-flow.json](sample-nifi-2.7-flow.json)** - Sample flow in NiFi 2.7+ JSON format
- **[NIFI_2.7_IMPORT_GUIDE.md](NIFI_2.7_IMPORT_GUIDE.md)** - Complete guide for importing and using NiFi 2.7 flows

## Quick Start

### For NiFi 2.7.2 and later (JSON format)
1. Read the [NiFi 2.7 Import Guide](NIFI_2.7_IMPORT_GUIDE.md)
2. Download [sample-nifi-2.7-flow.json](sample-nifi-2.7-flow.json)
3. Import via NiFi UI: Right-click → Upload Process Group → Select file
4. Start and schedule the flow

### What's New in NiFi 2.7?
NiFi 2.7 introduced a major change in flow configuration format:
- **Old format** (pre-2.7): XML-based (`flow.xml.gz`)
- **New format** (2.7+): JSON-based (`flow.json.gz`)

The JSON format provides better readability, easier version control, and improved compatibility with modern tooling.

## Repository Structure
```
.
├── README.md                      # This file
├── NIFI_2.7_IMPORT_GUIDE.md      # Detailed import and usage guide
└── sample-nifi-2.7-flow.json     # Sample NiFi 2.7 flow
```

## About the Sample Flow

The sample flow includes:
- **GenerateFlowFile** - Creates test data every 60 seconds
- **LogAttribute** - Logs flow file attributes
- **UpdateAttribute** - Adds custom attributes

This demonstrates the basic building blocks of a NiFi flow and can be used as a template for more complex flows.

## Contributing

To contribute flows to this registry:
1. Export your flow from NiFi 2.7+
2. Add descriptive comments and documentation
3. Submit a pull request with your flow and documentation

## License

This repository is for demonstration and educational purposes.
