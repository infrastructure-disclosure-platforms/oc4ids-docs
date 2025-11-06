# OC4IDS Implementation Guide

**Complete Guide to Publishing Infrastructure Transparency Data**

Version: 1.0
Last Updated: 2025-01-06
Target Standard: OC4IDS 1.1

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [What is OC4IDS?](#what-is-oc4ids)
3. [CoST Datastore and Registry](#cost-datastore-and-registry)
4. [Architecture Overview](#architecture-overview)
5. [Publisher Requirements](#publisher-requirements)
6. [Data Model Requirements](#data-model-requirements)
7. [Platform-Agnostic Implementation](#platform-agnostic-implementation)
8. [Key Transformation Patterns](#key-transformation-patterns)
9. [OC4IDS Schema Compliance](#oc4ids-schema-compliance)
10. [Validation and Testing](#validation-and-testing)
11. [CoST Datastore Submission](#cost-datastore-submission)
12. [Troubleshooting](#troubleshooting)
13. [Reference Implementation](#reference-implementation)
14. [Resources](#resources)

---

## Executive Summary

This guide provides step-by-step instructions for implementing an OC4IDS (Open Contracting for Infrastructure Data Standards) compliant API endpoint that publishes infrastructure project data for transparency initiatives.

**What you'll accomplish:**
- Create a public API endpoint serving valid OC4IDS 1.1 JSON
- Transform your project/contract data to meet OC4IDS schema requirements
- Publish to the CoST IDS Datastore for global transparency
- Enable daily automated data fetching and validation

**Time Investment:** 2-5 days for initial implementation
**Technical Level:** Intermediate backend development

---

## What is OC4IDS?

**Open Contracting for Infrastructure Data Standards (OC4IDS)** is a specialized extension of the Open Contracting Data Standard (OCDS) designed specifically for infrastructure projects.

### Why OC4IDS Matters

- **Transparency**: Enables public oversight of infrastructure investments
- **Accountability**: Tracks projects through their entire lifecycle
- **Standardization**: Common format across countries and organizations
- **Interoperability**: Works with existing OCDS tools and platforms
- **Analysis**: Enables data-driven insights into infrastructure delivery

### Key Features

- **Project Lifecycle Tracking**: From identification through decommissioning
- **Procurement Integration**: Links projects to tenders and contracts
- **Budget and Cost Tracking**: Monitors financial performance
- **Geographic Data**: Maps infrastructure locations
- **Stakeholder Transparency**: Identifies all parties involved

---

## CoST Datastore and Registry

The [CoST Datastore and Registry](https://datastore.infrastructuretransparency.org/) is the central repository for OC4IDS data published by CoST members worldwide.

### How It Works

1. **Daily Fetching**: The datastore automatically fetches data from your endpoint every 24 hours
2. **Schema Validation**: Each fetch is validated against the OC4IDS 1.1 schema
3. **Fallback Mechanism**: If validation fails or your endpoint is unavailable, the datastore serves the most recent valid dataset
4. **Multiple Formats**: Users can download data in JSON, CSV, or spreadsheet formats
5. **Public Access**: All validated datasets are openly licensed and publicly available

### Benefits of Publication

- **Global Visibility**: Your infrastructure data reaches international audience
- **Compliance**: Meets CoST transparency requirements
- **Tools Access**: Data works with OCDS analysis tools
- **Quality Assurance**: Daily validation ensures data quality
- **Format Flexibility**: Users access data in their preferred format

---

## Architecture Overview

### High-Level Design

```
┌─────────────────┐
│  Your Database  │
│  (Projects,     │
│   Contracts,    │
│   Tenders)      │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────┐
│    Data Transformation Layer    │
│  • Query published projects     │
│  • Fetch related contracts      │
│  • Map to OC4IDS schema         │
│  • Clean invalid fields         │
│  • Validate structure           │
└────────┬────────────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│      OC4IDS API Endpoint        │
│   GET /api/oc4ids-data          │
│   Returns: JSON (OC4IDS 1.1)    │
└────────┬────────────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│    CoST Datastore (Daily)       │
│  • Fetches your data            │
│  • Validates against schema     │
│  • Publishes if valid           │
│  • Serves in multiple formats   │
└─────────────────────────────────┘
```

### Core Components

1. **Data Query Layer**: Retrieves published projects and related data
2. **Transformation Engine**: Converts internal data to OC4IDS format
3. **Validation Layer**: Ensures schema compliance before serving
4. **API Endpoint**: Publicly accessible URL serving JSON
5. **Caching (Optional)**: Improves performance for frequent requests

---

## Publisher Requirements

### Technical Requirements

✅ **Public API Endpoint**
- Must be publicly accessible (no authentication required for OC4IDS endpoint)
- HTTPS recommended but not required
- Must return `Content-Type: application/json`

✅ **Schema Validity**
- Must validate against OC4IDS 1.1 JSON Schema
- No additional fields outside the schema
- All required fields must be present

✅ **Data Freshness**
- Should reflect current project status
- Update at least daily (to match CoST fetch frequency)

✅ **Availability**
- Endpoint should be available 99%+ of the time
- If unavailable, CoST serves last valid dataset

✅ **Open Licensing**
- Data must be openly licensed (CC-BY, OGL, etc.)
- Licensing information in publisher object

### Data Requirements

✅ **Minimum Dataset**
- At least one published project
- Project must have: id, title, status
- Valid project status from OC4IDS codelist

✅ **Recommended Fields**
- Budget information
- Period (start/end dates)
- Locations (geographic data)
- Parties (stakeholders)
- Contracting processes (tenders/contracts)

---

## Data Model Requirements

### Top-Level OC4IDS Package Structure

```json
{
  "version": "1.1",
  "publishedDate": "2025-01-06T10:30:00.000Z",
  "publisher": {
    "name": "Your Organization Name",
    "scheme": "Country-specific scheme",
    "uid": "Unique organization ID",
    "uri": "https://your-organization.gov"
  },
  "uri": "https://your-api.com/oc4ids-data",
  "projects": [
    // Array of project objects
  ]
}
```

### Project Object (Minimal)

```json
{
  "id": "oc4ids-prefix-project-001",
  "title": "Highway Construction Project",
  "description": "Construction of 50km highway connecting City A to City B",
  "status": "implementation",
  "sector": ["transport"],
  "period": {
    "startDate": "2024-01-01T00:00:00.000Z",
    "endDate": "2025-12-31T23:59:59.999Z"
  },
  "budget": {
    "amount": {
      "amount": 10000000,
      "currency": "USD"
    }
  }
}
```

### Project Object (Complete)

```json
{
  "id": "oc4ids-98ri7r-project-001",
  "title": "Maputo-Beira Highway Expansion",
  "description": "Expansion and rehabilitation of 250km highway",
  "purpose": "Improve transportation infrastructure to support economic growth",
  "status": "implementation",
  "sector": ["transport", "economic"],
  "period": {
    "startDate": "2024-01-01T00:00:00.000Z",
    "endDate": "2025-12-31T23:59:59.999Z"
  },
  "budget": {
    "amount": {
      "amount": 50000000,
      "currency": "MZN"
    },
    "approvalDate": "2023-12-15T00:00:00.000Z"
  },
  "locations": [
    {
      "id": "location-001",
      "description": "Maputo Province to Sofala Province",
      "address": {
        "locality": "Maputo",
        "region": "South Region"
      },
      "geometry": {
        "type": "LineString",
        "coordinates": [[32.5732, -25.9655], [34.8516, -19.8433]]
      }
    }
  ],
  "parties": [
    {
      "id": "fundo-de-estradas",
      "name": "Fundo de Estradas",
      "roles": ["buyer"],
      "contactPoint": {
        "name": "João Silva",
        "email": "joao.silva@fe.gov.mz",
        "telephone": "+258 21 123 4567"
      }
    },
    {
      "id": "acme-construction",
      "name": "ACME Construction Ltd",
      "roles": ["supplier"],
      "contactPoint": {
        "email": "contact@acme.co.mz",
        "telephone": "+258 84 123 4567"
      }
    }
  ],
  "documents": [
    {
      "id": "env-impact-001",
      "documentType": "environmentalImpact",
      "title": "Environmental Impact Assessment",
      "description": "Comprehensive environmental assessment completed",
      "url": "https://docs.example.com/env-impact.pdf",
      "datePublished": "2023-11-01T00:00:00.000Z"
    }
  ],
  "contractingProcesses": [
    {
      "id": "tender-001",
      "summary": {
        "title": "Highway Construction Tender",
        "description": "Tender for highway expansion works",
        "status": "closed"
      },
      "releases": [
        {
          "id": "tender-001",
          "date": "2023-08-01T00:00:00.000Z",
          "tag": ["tender"],
          "url": "https://your-api.com/oc4ids-data?tenderId=tender-001"
        }
      ]
    },
    {
      "id": "contract-001",
      "summary": {
        "title": "Construction Contract",
        "status": "active",
        "contractValue": {
          "amount": 48000000,
          "currency": "MZN"
        },
        "contractPeriod": {
          "startDate": "2024-01-15T00:00:00.000Z",
          "endDate": "2025-10-31T23:59:59.999Z"
        },
        "suppliers": [
          {
            "id": "acme-construction",
            "name": "ACME Construction Ltd"
          }
        ],
        "finalValue": {
          "amount": 52000000,
          "currency": "MZN"
        }
      },
      "releases": [
        {
          "id": "contract-001-release-1",
          "date": "2024-01-15T00:00:00.000Z",
          "tag": ["contract"],
          "url": "https://your-api.com/oc4ids-data?contractId=contract-001"
        }
      ]
    }
  ],
  "completion": {
    "endDate": "2025-12-31T00:00:00.000Z",
    "finalValue": {
      "amount": 52000000,
      "currency": "MZN"
    },
    "scope": "All project objectives met. Highway constructed to specifications."
  }
}
```

---

## Platform-Agnostic Implementation

### Step 1: Create API Endpoint

#### Node.js / Express Example

```javascript
const express = require('express');
const app = express();

app.get('/api/oc4ids-data', async (req, res) => {
  try {
    // 1. Query published projects from database
    const projects = await getPublishedProjects();

    // 2. For each project, fetch related contracts and tenders
    const enrichedProjects = await enrichProjectsWithRelatedData(projects);

    // 3. Transform to OC4IDS format
    const oc4idsProjects = enrichedProjects.map(transformToOC4IDS);

    // 4. Build OC4IDS package
    const oc4idsData = {
      version: '1.1',
      publishedDate: new Date().toISOString(),
      publisher: {
        name: 'Your Organization',
        scheme: 'COUNTRY-ORG',
        uid: 'ORG-ID',
        uri: 'https://your-org.gov'
      },
      uri: `${req.protocol}://${req.get('host')}/api/oc4ids-data`,
      projects: oc4idsProjects
    };

    // 5. Set headers and return
    res.setHeader('Content-Type', 'application/json; charset=utf-8');
    res.setHeader('Cache-Control', 'public, max-age=300'); // 5 min cache
    res.json(oc4idsData);

  } catch (error) {
    console.error('OC4IDS generation error:', error);
    res.status(500).json({ error: 'Failed to generate OC4IDS data' });
  }
});

app.listen(3000, () => console.log('OC4IDS API running on port 3000'));
```

#### Python / Flask Example

```python
from flask import Flask, jsonify, request
from datetime import datetime
import json

app = Flask(__name__)

@app.route('/api/oc4ids-data', methods=['GET'])
def oc4ids_data():
    try:
        # 1. Query published projects from database
        projects = get_published_projects()

        # 2. Enrich with related data
        enriched_projects = enrich_projects_with_related_data(projects)

        # 3. Transform to OC4IDS format
        oc4ids_projects = [transform_to_oc4ids(p) for p in enriched_projects]

        # 4. Build OC4IDS package
        oc4ids_data = {
            'version': '1.1',
            'publishedDate': datetime.utcnow().isoformat() + 'Z',
            'publisher': {
                'name': 'Your Organization',
                'scheme': 'COUNTRY-ORG',
                'uid': 'ORG-ID',
                'uri': 'https://your-org.gov'
            },
            'uri': f'{request.url_root}api/oc4ids-data',
            'projects': oc4ids_projects
        }

        # 5. Return with proper headers
        response = jsonify(oc4ids_data)
        response.headers['Cache-Control'] = 'public, max-age=300'
        return response

    except Exception as error:
        print(f'OC4IDS generation error: {error}')
        return jsonify({'error': 'Failed to generate OC4IDS data'}), 500

if __name__ == '__main__':
    app.run(port=3000)
```

#### PHP Example

```php
<?php
header('Content-Type: application/json; charset=utf-8');
header('Cache-Control: public, max-age=300');

try {
    // 1. Query published projects from database
    $projects = getPublishedProjects();

    // 2. Enrich with related data
    $enrichedProjects = enrichProjectsWithRelatedData($projects);

    // 3. Transform to OC4IDS format
    $oc4idsProjects = array_map('transformToOC4IDS', $enrichedProjects);

    // 4. Build OC4IDS package
    $oc4idsData = [
        'version' => '1.1',
        'publishedDate' => gmdate('Y-m-d\TH:i:s.000\Z'),
        'publisher' => [
            'name' => 'Your Organization',
            'scheme' => 'COUNTRY-ORG',
            'uid' => 'ORG-ID',
            'uri' => 'https://your-org.gov'
        ],
        'uri' => 'https://your-api.com/api/oc4ids-data',
        'projects' => $oc4idsProjects
    ];

    // 5. Output JSON
    echo json_encode($oc4idsData, JSON_PRETTY_PRINT | JSON_UNESCAPED_SLASHES);

} catch (Exception $error) {
    http_response_code(500);
    echo json_encode(['error' => 'Failed to generate OC4IDS data']);
}
?>
```

### Step 2: Query Published Projects

```javascript
async function getPublishedProjects() {
  // Example using SQL database
  const query = `
    SELECT * FROM projects
    WHERE is_published = true
    AND status IS NOT NULL
    ORDER BY created_at DESC
  `;

  const projects = await database.query(query);
  return projects;
}
```

### Step 3: Enrich with Related Data

```javascript
async function enrichProjectsWithRelatedData(projects) {
  const projectIds = projects.map(p => p.id);

  // Fetch contracts for all projects (batch query)
  const contracts = await database.query(`
    SELECT * FROM contracts
    WHERE project_id IN (?)
  `, [projectIds]);

  // Fetch tenders for all projects
  const tenders = await database.query(`
    SELECT * FROM tenders
    WHERE project_id IN (?)
  `, [projectIds]);

  // Group by project_id
  const contractsByProject = groupBy(contracts, 'project_id');
  const tendersByProject = groupBy(tenders, 'project_id');

  // Attach to projects
  return projects.map(project => ({
    ...project,
    contracts: contractsByProject[project.id] || [],
    tenders: tendersByProject[project.id] || []
  }));
}

function groupBy(array, key) {
  return array.reduce((result, item) => {
    (result[item[key]] = result[item[key]] || []).push(item);
    return result;
  }, {});
}
```

---

## Key Transformation Patterns

### Pattern 1: Status Mapping

OC4IDS uses specific codelists for statuses. Map your internal statuses:

```javascript
function mapProjectStatus(internalStatus) {
  const statusMap = {
    'planning': 'identification',
    'design': 'preparation',
    'tendering': 'preparation',
    'construction': 'implementation',
    'in_progress': 'implementation',
    'finished': 'implementation',
    'closed': 'implementation',
    'cancelled': 'cancelled',
    'decommissioning': 'implementation'
  };

  return statusMap[internalStatus?.toLowerCase()] || 'identification';
}

function mapContractingProcessStatus(status, type) {
  // Valid OC4IDS values: preAward, active, closed

  if (type === 'tender') {
    const tenderMap = {
      'draft': 'preAward',
      'planning': 'preAward',
      'published': 'preAward',
      'active': 'preAward',
      'awarded': 'active',
      'complete': 'closed',
      'closed': 'closed',
      'cancelled': 'closed'
    };
    return tenderMap[status?.toLowerCase()] || 'preAward';
  }

  if (type === 'contract') {
    const contractMap = {
      'pending': 'active',
      'signed': 'active',
      'active': 'active',
      'execution': 'active',
      'completed': 'closed',
      'terminated': 'closed',
      'cancelled': 'closed'
    };
    return contractMap[status?.toLowerCase()] || 'active';
  }

  return 'active';
}
```

### Pattern 2: Date Formatting

OC4IDS requires ISO 8601 format:

```javascript
function formatDate(date) {
  if (!date) return undefined;

  try {
    // Handle different date types
    let jsDate;

    if (date instanceof Date) {
      jsDate = date;
    } else if (typeof date === 'string') {
      jsDate = new Date(date);
    } else if (date.toDate && typeof date.toDate === 'function') {
      // Firestore Timestamp
      jsDate = date.toDate();
    } else if (date.seconds) {
      // Firestore Timestamp object
      jsDate = new Date(date.seconds * 1000);
    } else {
      return undefined;
    }

    // Validate date
    if (isNaN(jsDate.getTime())) {
      return undefined;
    }

    // Return ISO 8601 format
    return jsDate.toISOString();

  } catch (error) {
    console.warn('Date formatting error:', error);
    return undefined;
  }
}
```

### Pattern 3: Party Transformation

```javascript
function transformParties(project, contracts) {
  const parties = [];

  // Add procuring entity (buyer)
  if (project.procuring_entity) {
    parties.push({
      id: generatePartyId(project.procuring_entity.name),
      name: project.procuring_entity.name,
      roles: ['buyer'],
      contactPoint: {
        name: project.procuring_entity.contact_person,
        email: project.procuring_entity.email,
        telephone: project.procuring_entity.phone
      }
    });
  }

  // Add contractors (suppliers)
  contracts.forEach(contract => {
    if (contract.contractor?.name) {
      const existingParty = parties.find(p => p.name === contract.contractor.name);
      if (!existingParty) {
        parties.push({
          id: generatePartyId(contract.contractor.name),
          name: contract.contractor.name,
          roles: ['supplier'],
          contactPoint: {
            email: contract.contractor.email,
            telephone: contract.contractor.phone
          }
        });
      }
    }
  });

  return parties;
}

function generatePartyId(name) {
  return name
    .toLowerCase()
    .replace(/[^a-z0-9]+/g, '-')
    .replace(/^-|-$/g, '');
}
```

### Pattern 4: Contracting Processes

```javascript
function buildContractingProcesses(project, contracts, tenders, baseUrl) {
  const processes = [];

  // Add tenders
  tenders.forEach(tender => {
    processes.push({
      id: tender.id,
      summary: {
        title: tender.title,
        description: tender.description || '',
        status: mapContractingProcessStatus(tender.status, 'tender')
      },
      releases: [{
        id: tender.id,
        date: formatDate(tender.start_date) || new Date().toISOString(),
        tag: ['tender'],
        url: `${baseUrl}/oc4ids-data?tenderId=${tender.id}`
      }]
    });
  });

  // Add contracts
  contracts.forEach(contract => {
    const summary = {
      title: contract.title,
      description: contract.description || '',
      status: mapContractingProcessStatus(contract.status, 'contract')
    };

    // Add contract value
    if (contract.value) {
      summary.contractValue = {
        amount: Number(contract.value.amount) || 0,
        currency: contract.value.currency || 'USD'
      };
    }

    // Add contract period
    const startDate = formatDate(contract.start_date);
    const endDate = formatDate(contract.end_date);
    if (startDate || endDate) {
      summary.contractPeriod = { startDate, endDate };
    }

    // Add suppliers
    if (contract.contractor?.name) {
      summary.suppliers = [{
        id: generatePartyId(contract.contractor.name),
        name: contract.contractor.name
      }];
    }

    // Add final value (if amended)
    if (contract.final_value) {
      summary.finalValue = {
        amount: Number(contract.final_value.amount) || 0,
        currency: contract.final_value.currency || 'USD'
      };
    }

    processes.push({
      id: contract.id,
      summary,
      releases: [{
        id: `${contract.id}-release-1`,
        date: formatDate(contract.signed_date) || new Date().toISOString(),
        tag: ['contract'],
        url: `${baseUrl}/oc4ids-data?contractId=${contract.id}`
      }]
    });
  });

  return processes;
}
```

### Pattern 5: Data Cleaning

Remove undefined/null values to comply with schema:

```javascript
function cleanForOC4IDS(obj) {
  const cleaned = {};

  for (const [key, value] of Object.entries(obj)) {
    // Skip undefined, null, empty strings
    if (value === undefined || value === null || value === '') {
      continue;
    }

    // Handle arrays
    if (Array.isArray(value)) {
      if (value.length === 0) continue;

      const cleanedArray = value
        .map(item => typeof item === 'object' ? cleanForOC4IDS(item) : item)
        .filter(item => item !== undefined && item !== null && item !== '');

      if (cleanedArray.length > 0) {
        cleaned[key] = cleanedArray;
      }
      continue;
    }

    // Handle nested objects
    if (typeof value === 'object' && !(value instanceof Date)) {
      const cleanedNested = cleanForOC4IDS(value);
      if (Object.keys(cleanedNested).length > 0) {
        cleaned[key] = cleanedNested;
      }
      continue;
    }

    // Include valid primitive values
    cleaned[key] = value;
  }

  return cleaned;
}
```

---

## OC4IDS Schema Compliance

### Required Fields (Top-Level)

```javascript
{
  "version": "1.1",              // REQUIRED: Must be "1.1"
  "publishedDate": "ISO 8601",   // REQUIRED: When data was published
  "publisher": {                 // REQUIRED: Organization details
    "name": "string",            // REQUIRED
    "uid": "string"              // REQUIRED
  },
  "projects": []                 // REQUIRED: Array of projects
}
```

### Required Fields (Project)

```javascript
{
  "id": "string",                // REQUIRED: Unique project identifier
  "title": "string",             // REQUIRED: Project name
  "status": "codelist value"     // REQUIRED: From projectStatus codelist
}
```

### Valid Project Status Values

From OC4IDS projectStatus codelist:
- `identification` - Project is being identified
- `preparation` - Project preparation phase
- `implementation` - Project construction/execution
- `cancelled` - Project has been cancelled

### Valid ContractingProcess Status Values

From OC4IDS contractingProcessStatus codelist:
- `preAward` - Before contract award (tenders, planning)
- `active` - Contract signed and active
- `closed` - Contract completed or terminated

### Common Schema Violations

❌ **Additional Fields** - Fields not in the OC4IDS schema
```javascript
// WRONG - projectSector is not valid
{
  "id": "proj-001",
  "projectSector": {  // ❌ Not in schema
    "scheme": "OC4IDS",
    "id": "transport"
  }
}

// CORRECT - Use sector array
{
  "id": "proj-001",
  "sector": ["transport"]  // ✅ Valid OC4IDS field
}
```

❌ **Tender in Summary** - Tender data should be in releases, not summary
```javascript
// WRONG
{
  "id": "cp-001",
  "summary": {
    "title": "Process",
    "tender": {  // ❌ Not allowed in summary
      "value": 1000000
    }
  }
}

// CORRECT
{
  "id": "cp-001",
  "summary": {
    "title": "Process",
    "status": "active"
  },
  "releases": [{
    "id": "tender-001",
    "tag": ["tender"],  // ✅ Tender info in releases
    "url": "..."
  }]
}
```

❌ **Contracts Array in Summary** - Flatten contract fields into summary
```javascript
// WRONG
{
  "summary": {
    "contracts": [{  // ❌ Not allowed
      "value": { "amount": 1000000 }
    }]
  }
}

// CORRECT
{
  "summary": {
    "contractValue": {  // ✅ Flatten into summary
      "amount": 1000000,
      "currency": "USD"
    },
    "contractPeriod": {
      "startDate": "2024-01-01T00:00:00.000Z",
      "endDate": "2025-12-31T00:00:00.000Z"
    }
  }
}
```

---

## Validation and Testing

### Pre-Deployment Validation

#### 1. Use the OC4IDS Validator

```bash
# Install OC4IDS tools
npm install -g oc4ids-validator

# Validate your JSON output
curl https://your-api.com/oc4ids-data | oc4ids-validator
```

#### 2. Manual Schema Check

```javascript
const Ajv = require('ajv');
const addFormats = require('ajv-formats');

async function validateOC4IDS(data) {
  // Fetch OC4IDS 1.1 schema
  const response = await fetch('https://standard.open-contracting.org/infrastructure/schema/1__1__0/project-package-schema.json');
  const schema = await response.json();

  // Validate
  const ajv = new Ajv({ allErrors: true });
  addFormats(ajv);
  const validate = ajv.compile(schema);
  const valid = validate(data);

  if (!valid) {
    console.error('Validation errors:', validate.errors);
    return false;
  }

  console.log('✅ Valid OC4IDS 1.1 data');
  return true;
}
```

#### 3. Test Data Example

```javascript
const testOC4IDSData = {
  version: '1.1',
  publishedDate: new Date().toISOString(),
  publisher: {
    name: 'Test Organization',
    scheme: 'TEST',
    uid: 'TEST-001',
    uri: 'https://test.org'
  },
  uri: 'https://test-api.com/oc4ids-data',
  projects: [{
    id: 'oc4ids-test-project-001',
    title: 'Test Highway Project',
    description: 'Test project for validation',
    status: 'identification',
    sector: ['transport'],
    period: {
      startDate: '2024-01-01T00:00:00.000Z',
      endDate: '2025-12-31T23:59:59.999Z'
    },
    budget: {
      amount: {
        amount: 1000000,
        currency: 'USD'
      }
    }
  }]
};

// Test validation
validateOC4IDS(testOC4IDSData).then(valid => {
  if (valid) {
    console.log('Ready for publication!');
  }
});
```

### Testing Checklist

- [ ] Endpoint is publicly accessible
- [ ] Returns valid JSON
- [ ] Content-Type header is `application/json`
- [ ] No authentication required
- [ ] Validates against OC4IDS 1.1 schema
- [ ] No additional fields outside schema
- [ ] All dates in ISO 8601 format
- [ ] Status values from valid codelists
- [ ] Tender data in releases (not summary)
- [ ] Contract fields flattened in summary
- [ ] All required fields present
- [ ] Response time < 30 seconds
- [ ] Handles errors gracefully

---

## CoST Datastore Submission

### Step 1: Verify Your Endpoint

Before submitting, ensure:
```bash
# Test your endpoint
curl https://your-api.com/oc4ids-data | jq

# Check it validates
curl https://your-api.com/oc4ids-data | oc4ids-validator

# Verify it's publicly accessible (test from external network)
```

### Step 2: Register with CoST

1. **Contact CoST**: Reach out to your national CoST program or CoST International
2. **Provide Endpoint URL**: Share your OC4IDS API endpoint URL
3. **Confirm Licensing**: Specify your open data license (e.g., CC-BY 4.0)
4. **Provide Organization Details**: Name, country, contact information

### Step 3: CoST Verification

CoST will:
1. Test your endpoint accessibility
2. Validate data against OC4IDS 1.1 schema
3. Verify data quality and completeness
4. Add your endpoint to the daily fetch schedule

### Step 4: Monitor Publication

Once registered:
- Check [CoST Datastore](https://datastore.infrastructuretransparency.org/) for your organization
- Your data appears after first successful daily fetch
- Monitor for validation errors (CoST will notify you)
- Update your endpoint as needed

### What Happens Daily

```
00:00 UTC - CoST Datastore initiates daily fetch
   ↓
CoST fetches: GET https://your-api.com/oc4ids-data
   ↓
Validates against OC4IDS 1.1 schema
   ↓
If VALID:
  - Updates datastore with new data
  - Makes available in JSON/CSV/spreadsheet
  - Your data is live!
   ↓
If INVALID or UNAVAILABLE:
  - Logs error
  - Keeps serving last valid dataset
  - Notifies publisher (if configured)
```

---

## Troubleshooting

### Issue 1: "Additional fields detected"

**Problem**: Validator reports fields not in OC4IDS schema

**Solution**: Remove or rename fields
```javascript
// BEFORE (with additional fields)
{
  "id": "proj-001",
  "projectSector": { "scheme": "...", "id": "..." },  // ❌
  "summary": {
    "numberOfTenderers": 5  // ❌
  }
}

// AFTER (schema compliant)
{
  "id": "proj-001",
  "sector": ["transport"],  // ✅ Use simple array
  "summary": {
    "status": "active"
    // Removed numberOfTenderers
  }
}
```

### Issue 2: "Invalid status value"

**Problem**: Status not from valid codelist

**Solution**: Map to valid OC4IDS status
```javascript
// Valid projectStatus values
const validProjectStatuses = [
  'identification',
  'preparation',
  'implementation',
  'cancelled'
];

// Valid contractingProcessStatus values
const validContractingProcessStatuses = [
  'preAward',
  'active',
  'closed'
];

// Always validate before setting
function setProjectStatus(status) {
  const mapped = mapProjectStatus(status);
  if (!validProjectStatuses.includes(mapped)) {
    return 'identification';  // Safe default
  }
  return mapped;
}
```

### Issue 3: "Invalid date format"

**Problem**: Dates not in ISO 8601 format

**Solution**: Use proper date formatting
```javascript
// WRONG
{
  "startDate": "01/15/2024"  // ❌ US format
}

// CORRECT
{
  "startDate": "2024-01-15T00:00:00.000Z"  // ✅ ISO 8601
}

// Helper function
function toISO8601(date) {
  if (!date) return undefined;
  return new Date(date).toISOString();
}
```

### Issue 4: "Required field missing"

**Problem**: Missing required fields

**Solution**: Ensure all required fields present
```javascript
// Validation helper
function validateProject(project) {
  const errors = [];

  if (!project.id) errors.push('Missing required field: id');
  if (!project.title) errors.push('Missing required field: title');
  if (!project.status) errors.push('Missing required field: status');

  if (errors.length > 0) {
    console.error('Validation errors:', errors);
    return false;
  }

  return true;
}
```

### Issue 5: "Endpoint timeout"

**Problem**: API takes too long to respond

**Solution**: Optimize queries and add caching
```javascript
// Add response caching
const NodeCache = require('node-cache');
const cache = new NodeCache({ stdTTL: 300 }); // 5 min cache

app.get('/api/oc4ids-data', async (req, res) => {
  // Check cache first
  const cached = cache.get('oc4ids-data');
  if (cached) {
    return res.json(cached);
  }

  // Generate fresh data
  const data = await generateOC4IDSData();

  // Cache it
  cache.set('oc4ids-data', data);

  res.json(data);
});
```

### Issue 6: "Tender in wrong location"

**Problem**: Tender data in summary instead of releases

**Solution**: Move tender data to releases
```javascript
// WRONG
{
  "contractingProcesses": [{
    "summary": {
      "tender": { ... }  // ❌ Not allowed here
    }
  }]
}

// CORRECT
{
  "contractingProcesses": [{
    "summary": {
      "title": "Tender Process",
      "status": "preAward"
    },
    "releases": [{
      "id": "tender-001",
      "tag": ["tender"],  // ✅ Tender data here
      "url": "..."
    }]
  }]
}
```

---

## Reference Implementation

### Complete Transformation Function

```javascript
function transformProjectToOC4IDS(project, contracts, tenders, baseUrl) {
  // Build base project
  const oc4idsProject = {
    id: `oc4ids-${project.country_code}-${project.id}`,
    title: project.title || 'Untitled Project',
    description: project.description || '',
    status: mapProjectStatus(project.status)
  };

  // Add purpose (optional but recommended)
  if (project.purpose) {
    oc4idsProject.purpose = project.purpose;
  }

  // Add period
  if (project.start_date || project.end_date) {
    oc4idsProject.period = {};
    if (project.start_date) {
      oc4idsProject.period.startDate = formatDate(project.start_date);
    }
    if (project.end_date) {
      oc4idsProject.period.endDate = formatDate(project.end_date);
    }
  }

  // Add budget
  if (project.budget) {
    oc4idsProject.budget = {
      amount: {
        amount: Number(project.budget.amount) || 0,
        currency: project.budget.currency || 'USD'
      }
    };

    if (project.budget.approval_date) {
      oc4idsProject.budget.approvalDate = formatDate(project.budget.approval_date);
    }
  }

  // Add sectors (required, defaults to transport if missing)
  oc4idsProject.sector = project.sectors && project.sectors.length > 0
    ? project.sectors
    : ['transport'];

  // Add locations
  if (project.locations && project.locations.length > 0) {
    oc4idsProject.locations = project.locations.map((loc, index) => ({
      id: loc.id || `location-${index}`,
      description: loc.description || loc.name,
      address: {
        locality: loc.locality,
        region: loc.region
      },
      geometry: loc.geometry
    })).filter(loc => loc.description || loc.address || loc.geometry);
  }

  // Add parties
  oc4idsProject.parties = transformParties(project, contracts);

  // Add documents
  const documents = [];

  // Existing documents
  if (project.documents) {
    documents.push(...project.documents.map(doc => ({
      id: doc.id,
      documentType: doc.type || 'other',
      title: doc.title,
      description: doc.description,
      url: doc.url,
      datePublished: formatDate(doc.date_published)
    })));
  }

  // Environmental impact
  if (project.environmental_impact) {
    documents.push({
      id: `env-${project.id}`,
      documentType: 'environmentalImpact',
      title: 'Environmental Impact Assessment',
      description: project.environmental_impact
    });
  }

  // Social impact
  if (project.social_impact) {
    documents.push({
      id: `social-${project.id}`,
      documentType: 'socialImpact',
      title: 'Land and Settlement Impact Assessment',
      description: project.social_impact
    });
  }

  if (documents.length > 0) {
    oc4idsProject.documents = documents;
  }

  // Add contracting processes
  oc4idsProject.contractingProcesses = buildContractingProcesses(
    project,
    contracts,
    tenders,
    baseUrl
  );

  // Add completion data (if project completed)
  if (project.completion) {
    oc4idsProject.completion = {
      endDate: formatDate(project.completion.date),
      finalValue: {
        amount: Number(project.completion.final_value) || 0,
        currency: project.completion.currency || 'USD'
      },
      scope: project.completion.scope_description
    };
  }

  // Clean undefined/null values
  return cleanForOC4IDS(oc4idsProject);
}
```

### Complete API Example (Express.js)

```javascript
const express = require('express');
const app = express();

// Cache for performance
const NodeCache = require('node-cache');
const cache = new NodeCache({ stdTTL: 300 }); // 5 minutes

app.get('/api/oc4ids-data', async (req, res) => {
  try {
    // Check cache
    const cached = cache.get('oc4ids-full');
    if (cached) {
      res.setHeader('Content-Type', 'application/json; charset=utf-8');
      res.setHeader('Cache-Control', 'public, max-age=300');
      res.setHeader('X-Cache', 'HIT');
      return res.json(cached);
    }

    // 1. Query published projects
    const projects = await db.query(`
      SELECT * FROM projects
      WHERE is_published = true
      AND status IS NOT NULL
    `);

    const projectIds = projects.map(p => p.id);

    // 2. Fetch related contracts and tenders (batch queries)
    const [contracts, tenders] = await Promise.all([
      db.query('SELECT * FROM contracts WHERE project_id IN (?)', [projectIds]),
      db.query('SELECT * FROM tenders WHERE project_id IN (?)', [projectIds])
    ]);

    // Group by project
    const contractsByProject = groupBy(contracts, 'project_id');
    const tendersByProject = groupBy(tenders, 'project_id');

    // 3. Transform to OC4IDS
    const baseUrl = `${req.protocol}://${req.get('host')}`;
    const oc4idsProjects = projects.map(project => {
      return transformProjectToOC4IDS(
        project,
        contractsByProject[project.id] || [],
        tendersByProject[project.id] || [],
        baseUrl
      );
    });

    // 4. Build final package
    const oc4idsData = {
      version: '1.1',
      publishedDate: new Date().toISOString(),
      publisher: {
        name: 'Your Organization Name',
        scheme: 'COUNTRY-ORG',
        uid: 'ORG-123',
        uri: 'https://your-org.gov'
      },
      uri: `${baseUrl}/api/oc4ids-data`,
      projects: oc4idsProjects
    };

    // 5. Cache and return
    cache.set('oc4ids-full', oc4idsData);

    res.setHeader('Content-Type', 'application/json; charset=utf-8');
    res.setHeader('Cache-Control', 'public, max-age=300');
    res.setHeader('X-Cache', 'MISS');
    res.json(oc4idsData);

  } catch (error) {
    console.error('OC4IDS generation error:', error);
    res.status(500).json({
      error: 'Failed to generate OC4IDS data',
      message: error.message,
      timestamp: new Date().toISOString()
    });
  }
});

// Helper functions (from previous sections)
function groupBy(array, key) { /* ... */ }
function mapProjectStatus(status) { /* ... */ }
function mapContractingProcessStatus(status, type) { /* ... */ }
function formatDate(date) { /* ... */ }
function transformParties(project, contracts) { /* ... */ }
function buildContractingProcesses(project, contracts, tenders, baseUrl) { /* ... */ }
function cleanForOC4IDS(obj) { /* ... */ }

// Start server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`OC4IDS API running on port ${PORT}`);
  console.log(`Endpoint: http://localhost:${PORT}/api/oc4ids-data`);
});
```

---

## Resources

### Official OC4IDS Resources

- **OC4IDS 1.1 Documentation**: https://standard.open-contracting.org/infrastructure/1.1/en/
- **OC4IDS Schema**: https://standard.open-contracting.org/infrastructure/schema/1__1__0/
- **Project Package Schema**: https://standard.open-contracting.org/infrastructure/schema/1__1__0/project-package-schema.json
- **CoST Datastore**: https://datastore.infrastructuretransparency.org/
- **CoST International**: https://infrastructuretransparency.org/

### Validation Tools

- **OC4IDS Validator**: https://standard.open-contracting.org/infrastructure/validator/
- **OCDS Validator** (works with OC4IDS): https://review.standard.open-contracting.org/
- **JSON Schema Validator**: https://www.jsonschemavalidator.net/

### Code Examples

- **OC4IDS Examples Repository**: https://github.com/open-contracting-extensions/oc4ids-examples
- **This Implementation (Mozambique)**: Reference `functions/src/api/index.ts` in this repository

### Related Standards

- **OCDS (Open Contracting Data Standard)**: https://standard.open-contracting.org/
- **CoST IDS (Infrastructure Data Standard)**: https://infrastructuretransparency.org/our-approach/the-cost-infrastructure-data-standard/
- **GeoJSON**: https://geojson.org/ (for location geometry)

### Community Support

- **OC4IDS Discussion Forum**: https://github.com/open-contracting/infrastructure/discussions
- **OCDS Helpdesk**: data@open-contracting.org
- **CoST Support**: info@infrastructuretransparency.org

---

## Next Steps

### Immediate Actions (Week 1)

1. ✅ Set up basic API endpoint
2. ✅ Query and transform 1-2 test projects
3. ✅ Validate against OC4IDS schema
4. ✅ Fix any validation errors

### Short-term Goals (Weeks 2-4)

1. ✅ Transform all published projects
2. ✅ Add contracting processes (tenders/contracts)
3. ✅ Implement caching for performance
4. ✅ Register with CoST Datastore
5. ✅ Monitor first successful fetch

### Long-term Improvements (Ongoing)

1. ✅ Add more project metadata (purpose, completion data)
2. ✅ Improve geographic data (geometry, coordinates)
3. ✅ Enhance party information (full contact details)
4. ✅ Add comprehensive documentation (environmental/social assessments)
5. ✅ Monitor and maintain data quality

---

## License

This guide is provided under CC-BY 4.0. You are free to use, modify, and distribute with attribution.

---

**Questions or issues?** Open an issue in this repository or contact the OC4IDS community for support.

**Last updated**: 2025-01-06
