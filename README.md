![DK Hostmaster Logo](https://www.dk-hostmaster.dk/sites/default/files/dk-logo_0.png)

:warning: This document is not longer being updated, information has been lifted into the relevant service specifications :warning:

# DKHM RFC for Prepaid account information with the EPP Service

![Markdownlint Action](https://github.com/DK-Hostmaster/DKHM-RFC-Prepaid/workflows/Markdownlint%20Action/badge.svg)
![Spellcheck Action](https://github.com/DK-Hostmaster/DKHM-RFC-Prepaid/workflows/Spellcheck%20Action/badge.svg)

2020-11-23
Revision: 1.1

## Table of Contents

<!-- MarkdownTOC bracket=round levels="1,2,3,4" indent="  " autolink="true" autoanchor="true" -->

- [Introduction](#introduction)
  - [About this Document](#about-this-document)
  - [License](#license)
  - [Document History](#document-history)
  - [XML and XSD Examples](#xml-and-xsd-examples)
- [Description](#description)
  - [Fetch Balance](#fetch_balance)
  - [Domain name Application/Creation Failure](#domain_application_failure)
  - [Restore a Domain Name, suspended due to missing financial settlement](#restore)
- [XSD Definition](#xsd-definition)
- [References](#references)

<!-- /MarkdownTOC -->

<a id="introduction"></a>
## Introduction

This is a draft and proposal for the service integration for use of the mandatory prepaid account for registrars when using the  DK Hostmaster EPP and registrar portals/services for billable operations. The specification briefly touches on the registrar portal service, which mimicks the EPP service for consistency.

The overall [description of the concept][CONCEPT] of the registrar model offered by DK Hostmaster A/S provided as a general overview.

The operations currently classified as billable are:

- Domain name application/creation
- Domain name renewal
- Restoration from deletion, this is described in detail in the ["DKHM RFC for Restore Command"][DKHMRFCRESTORE]
- Restoration from suspension, also described in detail in the ["DKHM RFC for Restore Command"][DKHMRFCRESTORE]

The procedures for renewal and application/creation are not being changed, in regard to use and protocol. The business policies in relation to these operations, do however change, since the billing operation changes.

This mean that a new error scenario is introduced for creation/application, where an application/create request will be declined, in case of insufficient funds. The renewal operation is not subjected to this policy, please refer to the registrar contract for specific details as this is a technical document and not the authoritative source for business and policy rules.

All prices and amounts relating to currencies are provided in DKK, converted to the EPP currency type, using decimal point (`.`) and not decimal comma (`,`), which is the definition for the Danish locale.

<a id="about-this-document"></a>
### About this Document

We have adopted the term RFC (_Request For Comments_), due to the recognition in the term and concept, so this document is a process supporting document, aiming to serve the purpose of obtaining a common understanding of the proposed implementation and to foster discussion on the details of the implementation. The final specification will be lifted into the [DK Hostmaster EPP Service Specification][DKHMEPPSPEC] implementation and this document will be closed for comments and the document no longer be updated.

As specified in the introduction, this document is not the authoritative source for business and policy rules and possible discrepancies between this an any authoritative sources are regarded as errors in this document. This document is aimed at the technical specification and possible implementation and is an interpretation of authoritative sources and can therefor be erroneous.

<a id="license"></a>
### License

This document is copyright by DK Hostmaster A/S and is licensed under the MIT License, please see the separate LICENSE file for details.

<a id="document-history"></a>
### Document History

- 1.1 2020-11-23
  - Removal of restoration information, which has been collected in a separate RFC
  - Addition of additional links to resources
  - Correction to links pointing to redundant resources
  - Minor rephrasing and clarifications

- 1.0 2020-09-21
  - Initial revision

<a id="xml-and-xsd-examples"></a>
### XML and XSD Examples

All example XML files are available in the [DK Hostmaster EPP XSD repository][DKHMXSDSPEC].

The proposed extensions and XSD definitions are available in the [4.0][DKHMXSD4.0] revision of the DK Hostmaster XSD, which is currently marked as a  _pre-release_, unless these are separate extensions, where the original RFCs should be consulted. See: [RFC:3915] for extensions related to grace periods. The XSD definition related to balance originated from "[Balance Mapping for the Extensible Provisioning Protocol (EPP)][BALANCE]" and is included in this document.

The referenced XSD version is not deployed at this time and is only available in the [EPP XSD repository][DKHMXSDSPEC], it might be surpassed by a newer version upon deployment of the EPP service implementing the proposal, please refer to the revision of [EPP Service Specification][DKHMEPPSPEC] describing the implementation.

<a id="description"></a>
## Description

<a id="fetch_balance"></a>
### Fetch Balance

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    <command>
        <info>
            <balance:info xmlns:balance="http://www.verisign.com/epp/balance-1.0"/>
        </info>
        <clTRID>ABC-12345</clTRID>
    </command>
</epp>
```

Example lifted from "[Balance Mapping for the Extensible Provisioning Protocol (EPP)][BALANCE]" (see References).

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    <response>
        <result code="1000">
            <msg>Command completed successfully</msg>
        </result>
        <resData>
            <balance:infData xmlns:balance="http://www.verisign.com/epp/balance-1.0">
                <balance:creditLimit>1000.00</balance:creditLimit>
                <balance:balance>200.00</balance:balance>
                <balance:availableCredit>800.00</balance:availableCredit>
                <balance:creditThreshold>
                    <balance:fixed>500.00</balance:fixed>
                </balance:creditThreshold>
            </balance:infData>
        </resData>
        <trID>
            <clTRID>ABC-12345</clTRID>
            <svTRID>54322-XYZ</svTRID>
        </trID>
    </response>
</epp>
```

Example lifted from "[Balance Mapping for the Extensible Provisioning Protocol (EPP)][BALANCE]" (see References).

<a id="domain_application_failure"></a>
### Domain name Application/Creation Failure

As described in the introduction, the existing commands, which are categorized as billable are not changed. Due to the change to the billing procedure however, the application/create operation is extended with a error scenario, for when the prepaid account does not have sufficient funds.

The proposed error message is the following.

> 2104    "Billing failure"
>
> his response code MUST be returned when a server attempts
> to execute a billable operation and the command cannot be
> completed due to a client-billing failure.

Quoted from [RFC:5730].

<a id="restore"></a>
### Restore a Domain Name, suspended due to missing financial settlement

The ability to restore a domain from a suspended state, is mentioned in the introduction as a billable operation.

The proposal for the restore implementation is outlined in the separate RFC: ["DKHM RFC for Restore Command"][DKHMRFCRESTORE].

<a id="xsd-definition"></a>
## XSD Definition

The XSD definition supporting [RFC:3915], is included with [RFC:3915].

The XSD definition for supporting balance was lifted from "[Balance Mapping for the Extensible Provisioning Protocol (EPP)][BALANCE]".

```xsd
<?xml version="1.0" encoding="UTF-8"?>

<schema targetNamespace="http://www.verisign.com/epp/balance-1.0"
    xmlns:balance="http://www.verisign.com/epp/balance-1.0"
    xmlns="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified">

    <annotation>
        <documentation>
      Extensible Provisioning Protocol v1.0
      Verisign mapping for getting account balance and
      other financial information.
        </documentation>
    </annotation>

    <!--
Child elements found in EPP commands.
-->
    <!-- Empty balance:info command element -->
    <element name="info"/>

    <!--
Child response elements.
-->
    <element name="infData" type="balance:infDataType"/>

    <!--Child elements of the balance:infData element -->
    <complexType name="infDataType">
        <sequence>
            <element name="creditLimit" type="balance:currencyValueType"/>
            <element name="balance" type="balance:currencyValueType"/>
            <element name="availableCredit" type="balance:currencyValueType"/>
            <element name="creditThreshold" type="balance:thresholdType"/>
        </sequence>
    </complexType>

    <complexType name="thresholdType">
        <choice>
            <element name="fixed" type="balance:currencyValueType"/>
            <element name="percent" type="integer"/>
        </choice>
    </complexType>

    <simpleType name="currencyValueType">
        <restriction base="decimal">
            <fractionDigits value="2"/>
        </restriction>
    </simpleType>

    <!-- End of schema.-->
</schema>
```

XSD definition lifted from "[Balance Mapping for the Extensible Provisioning Protocol (EPP)][BALANCE]" (see References).

<a id="references"></a>
## References

1. ["New basis for collaboration between registrars and DK Hostmaster"][CONCEPT]
1. [DK Hostmaster EPP Service Specification][DKHMEPPSPEC]
1. [DK Hostmaster EPP Service XSD Repository][DKHMXSDSPEC]
1. ["DKHM RFC for Delete Domain EPP Command"][DKHMRFCDELETE]
1. ["DKHM RFC for Restore Command"][DKHMRFCRESTORE]
1. [RFC:3915 "Domain Registry Grace Period Mapping for the Extensible Provisioning Protocol (EPP)"][RFC:3915]
1. [RFC:5730 "Extensible Provisioning Protocol (EPP)"][RFC:5730]

[CONCEPT]: https://www.dk-hostmaster.dk/en/new-basis-collaboration-between-registrars-and-dk-hostmaster
[DKHMEPPSPEC]: https://github.com/DK-Hostmaster/epp-service-specification
[DKHMXSDSPEC]: https://github.com/DK-Hostmaster/epp-xsd-files
[DKHMRFCDELETE]: https://github.com/DK-Hostmaster/DKHM-RFC-Delete-Domain
[DKHMRFCRESTORE]: https://github.com/DK-Hostmaster/DKHM-RFC-Restore
[RFC:3915]: https://www.rfc-editor.org/rfc/rfc3915.html
[RFC:5730]: https://www.rfc-editor.org/rfc/rfc5730.html
[DKHMXSD4.0]: https://github.com/DK-Hostmaster/epp-xsd-files/blob/master/dkhm-4.0.xsd
[BALANCE]: https://www.verisign.com/assets/epp-sdk/verisign_epp-extension_balance_v01.html
