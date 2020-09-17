![DK Hostmaster Logo](https://www.dk-hostmaster.dk/sites/default/files/dk-logo_0.png)

# DKHM RFC for Prepaid account information with the EPP Service

![Markdownlint Action](https://github.com/DK-Hostmaster/DKHM-RFC-Prepaid/workflows/Markdownlint%20Action/badge.svg)
![Spellcheck Action](https://github.com/DK-Hostmaster/DKHM-RFC-Prepaid/workflows/Spellcheck%20Action/badge.svg)

2020-09-17
Revision: 1.0

## Table of Contents

<!-- MarkdownTOC bracket=round levels="1,2,3,4" indent="  " autolink="true" autoanchor="true" -->

- [Introduction](#introduction)
  - [About this Document](#about-this-document)
  - [License](#license)
  - [Document History](#document-history)
  - [XML and XSD Examples](#xml-and-xsd-examples)
- [Description](#description)
- [XSD Definition](#xsd-definition)
- [References](#references)

<!-- /MarkdownTOC -->

<a id="introduction"></a>
## Introduction

This is a draft and proposal for changes to the process for domain name deletion via the DK Hostmaster EPP portal/service. The specification briefly touches on the registrar portal service, which mimicks the EPP service for consistency.

The overall [description of the concept][CONCEPT] of the registrar model offered by DK Hostmaster A/S provided as a general overview, where this RFC digs into the details of the cancellation/deletion of domain names in the context of an implementation proposal.

<a id="about-this-document"></a>
### About this Document

We have adopted the term RFC (_Request For Comments_), due to the recognition in the term and concept, so this document is a process supporting document, aiming to serve the purpose of obtaining a common understanding of the proposed implementation and to foster discussion on the details of the implementation. The final specification will be lifted into the [DK Hostmaster EPP Service Specification][DKHMEPPSPEC] implementation and this document will be closed for comments and the document no longer be updated.

<a id="license"></a>
### License

This document is copyright by DK Hostmaster A/S and is licensed under the MIT License, please see the separate LICENSE file for details.

<a id="document-history"></a>
### Document History

- 1.0 2020-09-17
  - Initial revision

<a id="xml-and-xsd-examples"></a>
### XML and XSD Examples

All example XML files are available in the [DK Hostmaster EPP XSD repository][DKHMXSDSPEC].

The proposed extensions and XSD definitions are available in the  [3.2 candidate][DKHMXSD3.2] of the DK Hostmaster XSD, which is currently a draft and work in progress and marked as a  _pre-release_.

<a id="description"></a>
## Description

<a id="xsd-definition"></a>
## XSD Definition

<a id="references"></a>
## References

- [DK Hostmaster EPP Service Specification][DKHMEPPSPEC]
- [DK Hostmaster EPP Service XSD Repository][DKHMXSDSPEC]
- [RFC:5730 "Extensible Provisioning Protocol (EPP)"][RFC5730]

[RFC5730]: https://www.rfc-editor.org/rfc/rfc5730.html
[DKHMEPPSPEC]: https://github.com/DK-Hostmaster/epp-service-specification
[DKHMXSDSPEC]: https://github.com/DK-Hostmaster/epp-xsd-files
[CONCEPT]: https://www.dk-hostmaster.dk/en/new-basis-collaboration-between-registrars-and-dk-hostmaster
[DKHMXSD3.2]: https://github.com/DK-Hostmaster/epp-xsd-files/blob/master/dkhm-3.2.xsd
