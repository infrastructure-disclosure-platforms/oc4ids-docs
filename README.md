# OC4IDS Technical Implementation Guides

Technical documentation for developers building infrastructure transparency platforms using the Open Contracting for Infrastructure Data Standards (OC4IDS).

## Purpose

This repository provides **practical, production-ready implementation guides** for building OC4IDS-compliant disclosure platforms. Whether you're a government agency, development organization, or infrastructure transparency advocate, these guides help you publish standardized infrastructure project data that integrates with the global CoST Datastore.

**Key Difference**: While the [official OC4IDS documentation](https://standard.open-contracting.org/infrastructure/1.1/en/) defines the standard, these guides focus on **how to implement it** with real-world code examples, transformation patterns, and troubleshooting solutions.

## What is OC4IDS?

**Open Contracting for Infrastructure Data Standards (OC4IDS)** is a specialized data standard for publishing information about infrastructure projects throughout their lifecycle—from planning through completion. It enables transparency, accountability, and data-driven insights into how infrastructure investments are planned, procured, and delivered.

**Why it matters**: OC4IDS provides a common language for infrastructure data, enabling:
- Public oversight of infrastructure investments
- Cross-country comparisons and analysis
- Integration with existing Open Contracting Data Standard (OCDS) tools
- Automated validation and quality assurance through the CoST Datastore

Learn more: [OC4IDS 1.1 Standard Documentation](https://standard.open-contracting.org/infrastructure/1.1/en/)

## Repository Contents

### Available Guides

#### 📘 [OC4IDS Implementation Guide](docs/OC4IDS-IMPLEMENTATION-GUIDE.md)
Complete guide to publishing OC4IDS-compliant infrastructure project data.

**Contents**:
- Architecture and design patterns
- Platform-agnostic API implementation (Node.js, Python, PHP examples)
- Data model requirements and transformation patterns
- Schema validation and testing
- CoST Datastore integration and daily fetching
- Troubleshooting common issues
- Reference implementations

**Time to implement**: 2-5 days for initial API endpoint
**Technical level**: Intermediate backend development

### Future Guides (Planned)
- Advanced OC4IDS patterns (bulk data, versioning, amendments)
- Integration with specific platforms (Django, Laravel, Express.js)
- Data quality monitoring and maintenance
- Performance optimization for large datasets

_Want a specific guide? [Open an issue](../../issues) to suggest topics._

## Quick Start

### For Developers Ready to Implement

1. **Read the requirements** - Understand [publisher requirements](docs/OC4IDS-IMPLEMENTATION-GUIDE.md#publisher-requirements) (public API, schema compliance, data freshness)

2. **Design your transformation layer** - Review [data model requirements](docs/OC4IDS-IMPLEMENTATION-GUIDE.md#data-model-requirements) and [transformation patterns](docs/OC4IDS-IMPLEMENTATION-GUIDE.md#key-transformation-patterns)

3. **Build your API endpoint** - Follow [platform-specific examples](docs/OC4IDS-IMPLEMENTATION-GUIDE.md#platform-agnostic-implementation) for your stack

4. **Validate and publish** - Use [validation tools](docs/OC4IDS-IMPLEMENTATION-GUIDE.md#validation-and-testing) and [submit to CoST Datastore](docs/OC4IDS-IMPLEMENTATION-GUIDE.md#cost-datastore-submission)

**Estimated timeline**: 2-5 days for basic implementation, 1-2 weeks for production-ready endpoint with full project data

## Who Should Use These Guides

**These guides are for**:
- Backend developers implementing OC4IDS APIs
- Infrastructure project disclosure teams building transparency platforms
- CoST member countries establishing data publication systems
- Open data publishers in the infrastructure sector
- Development organizations supporting transparency initiatives

**You should be familiar with**:
- Backend API development (REST APIs, JSON)
- Database queries and data transformation
- Basic understanding of infrastructure project lifecycles
- Reading technical specifications

## Prerequisites

### Technical Requirements
- Backend development experience (Node.js, Python, PHP, or similar)
- Access to infrastructure project database
- Ability to deploy public API endpoints
- Basic understanding of JSON Schema validation

### Data Requirements
- Infrastructure project data (at minimum: project ID, title, status)
- Related contract and tender information (recommended)
- Budget and financial data (recommended)
- Geographic/location data (recommended)

### Infrastructure Requirements
- Public web hosting for API endpoint
- HTTPS capability (recommended but not required)
- Database access (MongoDB, PostgreSQL, MySQL, Firebase, or similar)

## Key Features of These Guides

✅ **Platform-Agnostic**: Examples for Node.js, Python, PHP—patterns work with any stack

✅ **Production-Ready**: Not toy examples—real code from deployed implementations

✅ **Schema Compliance First**: Focuses on passing OC4IDS 1.1 validation every time

✅ **CoST Integration**: Step-by-step guidance for CoST Datastore registration and daily fetching

✅ **Real-World Troubleshooting**: Common issues, validation errors, and proven solutions

✅ **Transformation Patterns**: Reusable patterns for mapping your data to OC4IDS format

## Related Resources

### Official OC4IDS Resources
- [OC4IDS 1.1 Standard Documentation](https://standard.open-contracting.org/infrastructure/1.1/en/)
- [OC4IDS JSON Schema](https://standard.open-contracting.org/infrastructure/schema/1__1__0/)
- [CoST Datastore and Registry](https://datastore.infrastructuretransparency.org/)
- [CoST International](https://infrastructuretransparency.org/)

### Validation Tools
- [OC4IDS Validator](https://standard.open-contracting.org/infrastructure/validator/)
- [OCDS Review Tool](https://review.standard.open-contracting.org/) (works with OC4IDS)
- [JSON Schema Validator](https://www.jsonschemavalidator.net/)

### Related Standards
- [Open Contracting Data Standard (OCDS)](https://standard.open-contracting.org/)
- [CoST Infrastructure Data Standard](https://infrastructuretransparency.org/our-approach/the-cost-infrastructure-data-standard/)
- [GeoJSON Specification](https://geojson.org/) (for location data)

### Community Support
- [OC4IDS GitHub Discussions](https://github.com/open-contracting/infrastructure/discussions)
- [OCDS Helpdesk](mailto:data@open-contracting.org)
- [CoST Support](mailto:info@infrastructuretransparency.org)

## Contributing

We welcome contributions from the OC4IDS implementation community!

### How to Contribute
- **Report Issues**: Found an error or unclear instruction? [Open an issue](../../issues)
- **Suggest Improvements**: Have a better way to explain a concept? [Submit a pull request](../../pulls)
- **Share Experiences**: Implemented OC4IDS? Share your patterns and lessons learned
- **Request Topics**: Need guidance on a specific implementation challenge? [Open an issue](../../issues)

### Contribution Guidelines
- Keep examples platform-agnostic where possible
- Test all code examples before submitting
- Follow existing documentation structure and style
- Include real-world context and rationale for patterns
- Link to official OC4IDS documentation where relevant

## Support

### Getting Help

**For implementation questions**:
1. Check the [troubleshooting section](docs/OC4IDS-IMPLEMENTATION-GUIDE.md#troubleshooting) in the main guide
2. Search [existing issues](../../issues) for similar questions
3. [Open a new issue](../../issues) with details about your implementation

**For standard clarifications**:
- [OC4IDS GitHub Discussions](https://github.com/open-contracting/infrastructure/discussions)
- [OCDS Helpdesk](mailto:data@open-contracting.org)

**For CoST Datastore registration**:
- Contact your national CoST program
- Reach out to [CoST International](mailto:info@infrastructuretransparency.org)

## License

This documentation is provided under the **Creative Commons Attribution 4.0 International License (CC-BY 4.0)**.

You are free to:
- **Share** — copy and redistribute the material
- **Adapt** — remix, transform, and build upon the material for any purpose

Under the following terms:
- **Attribution** — You must give appropriate credit and link to the license

See [LICENSE](LICENSE) for full terms.

---

**Questions or feedback?** [Open an issue](../../issues) or reach out to the OC4IDS community.

**Last updated**: 2025-01-06
