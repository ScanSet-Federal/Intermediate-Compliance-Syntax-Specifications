# ICS (Intermediate Compliance Syntax) License

## Overview

This repository contains the complete specification, grammar, and reference implementations for the ICS (Intermediate Compliance Syntax) language - a platform-agnostic universal language for compliance checking and validation across diverse computing environments.

## License Terms

### ICS Language Specification and EBNF Grammar

The ICS language specification, EBNF grammar, and associated documentation are released under the **MIT License**:

```
MIT License

Copyright (c) 2025 [Your Name/Organization]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### Definition Files and Examples

The ICS definition files (5000+ examples) included in this repository demonstrate compliance patterns across various platforms and systems. These files are released under the **Creative Commons Attribution 4.0 International (CC BY 4.0)** license:

You are free to:
- **Share** — copy and redistribute the material in any medium or format
- **Adapt** — remix, transform, and build upon the material for any purpose, even commercially

Under the following terms:
- **Attribution** — You must give appropriate credit, provide a link to the license, and indicate if changes were made. You may do so in any reasonable manner, but not in any way that suggests the licensor endorses you or your use.

### Standard Usage Rights

By releasing ICS as a public standard, we grant additional rights:

1. **Standard Implementation**: You may implement parsers, validators, transpilers, and other tools that process ICS syntax without restriction or royalty.

2. **Format Extension**: You may extend the ICS format for specific use cases, provided you:
   - Clearly document any extensions
   - Maintain backward compatibility with core ICS syntax where possible
   - Use a different name/version identifier for extended formats

3. **Commercial Use**: Commercial use of the ICS specification, grammar, and definition files is explicitly permitted and encouraged.

4. **Trademark**: "ICS" and "Intermediate Compliance Syntax" may be used to describe implementations and tools that conform to this specification.

## Attribution Requirements

When using ICS definition files or substantial portions of the specification, please include:

```
This work includes ICS (Intermediate Compliance Syntax) definition files
and/or specifications, available at: https://github.com/ScanSet-Federal/Intermediate-Compliance-Syntax-Specifications
```

## Contributions

Contributions to the ICS specification and definition files are welcome. By contributing, you agree to license your contributions under the same terms as this project.

### Contributing Guidelines

1. **Specification Changes**: Major changes to the EBNF grammar or core language features should be discussed via GitHub issues before implementation.

2. **Definition Files**: Additional ICS definition files should follow the established patterns and include appropriate documentation for their intended compliance use cases.

3. **Implementation Examples**: Parser implementations, validators, and transpilers are welcome as reference implementations.

## Compliance and Compatibility

### Universal Compliance Framework

The ICS definition files in this repository demonstrate compliance rules and validation logic for various platforms and systems. These definitions are designed to serve as:

- Reference implementations for compliance checking patterns
- Templates for creating new compliance rules
- Interoperability examples between different compliance frameworks
- Educational resources for compliance automation

While we strive for accuracy and completeness, users should:

- Validate ICS definitions against their specific compliance requirements
- Adapt definitions to match their organizational policies and standards
- Report any issues or improvements via GitHub issues
- Consider ICS definitions as starting points rather than final compliance authorities

### Version Compatibility

ICS follows semantic versioning:
- **Major versions** (X.0.0): Breaking changes to grammar or semantics
- **Minor versions** (X.Y.0): New features, backward compatible
- **Patch versions** (X.Y.Z): Bug fixes and clarifications

## Disclaimer

THE ICS SPECIFICATION AND DEFINITION FILES ARE PROVIDED FOR REFERENCE AND DEVELOPMENT PURPOSES. USERS ARE RESPONSIBLE FOR VALIDATING COMPLIANCE REQUIREMENTS AGAINST AUTHORITATIVE SOURCES AND ENSURING IMPLEMENTATIONS MEET THEIR SPECIFIC SECURITY, REGULATORY, AND COMPLIANCE NEEDS.

ICS serves as a universal format for expressing compliance rules but does not itself define compliance requirements. Organizations must ensure that their use of ICS-based systems meets applicable regulations, standards, and internal policies.

No warranty is provided regarding the accuracy, completeness, or fitness for purpose of any compliance definitions or implementations based on this specification.

## Contact

For questions about licensing, contributions, or the ICS specification:
- GitHub Issues: https://github.com/ScanSet-Federal/Intermediate-Compliance-Syntax-Specifications/issues
- Email: curtis@scanset.io
- Documentation: https://github.com/ScanSet-Federal/Intermediate-Compliance-Syntax-Specifications

---

**License Summary**: 
- ICS Language Specification: MIT License (maximum freedom for implementers)
- Definition Files: CC BY 4.0 (attribution required, commercial use allowed)
- Standard Usage: Unrestricted implementation and extension rights
- Contributions: Same license as project