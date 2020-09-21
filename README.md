![DK Hostmaster Logo](https://www.dk-hostmaster.dk/sites/default/files/dk-logo_0.png)

# DKHM RFC for Prepaid account information with the EPP Service

![Markdownlint Action](https://github.com/DK-Hostmaster/DKHM-RFC-Prepaid/workflows/Markdownlint%20Action/badge.svg)
![Spellcheck Action](https://github.com/DK-Hostmaster/DKHM-RFC-Prepaid/workflows/Spellcheck%20Action/badge.svg)

2020-09-21
Revision: 1.0

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
- Restoration from deletion, this is described in detail in the ["DKHM RFC for Delete Domain EPP Command"][DKHMRFCDELETE]
- Restoration from suspension, this will be covered in this RFC

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

- 1.0 2020-09-21
  - Initial revision

<a id="xml-and-xsd-examples"></a>
### XML and XSD Examples

All example XML files are available in the [DK Hostmaster EPP XSD repository][DKHMXSDSPEC].

The proposed extensions and XSD definitions are available in the  [3.2 candidate][DKHMXSD3.2] of the DK Hostmaster XSD, which is currently a draft and work in progress and marked as a  _pre-release_, unless these are separate extensions, where the original RFCs should be consulted. See: [RFC:3915][RFC3915] for extensions related to grace periods. The XSD definition related to balance originated from "[Balance Mapping for the Extensible Provisioning Protocol (EPP)][BALANCE]" and is included in this document.

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

Quoted from [RFC:5700][RFC5730].

<a id="restore"></a>
### Restore a Domain Name, suspended due to missing financial settlement

The ability to restore a domain from a suspended state, is mentioned in the introduction as a billable operation.

Restoration supports two scenarios.

1. Restoration from deletion, this is described in detail in the "[DKHM RFC for Delete Domain EPP Command][DKHMRFCDELETE]"
1. Restoration from suspension due to missing financial settlement

The procedure is the same and we aim to use the same extension, so the information provided in "[DKHM RFC for Delete Domain EPP Command][DKHMRFCDELETE]" is echoed here for clarity and due to the coherence to the prepaid concept.

As described in [RFC:3915][RFC3915], with a support for grace periods, it is possible to restore a domain name scheduled for deletion, (in the state `pendingDelete`), this is the scenario primarily covered in "[DKHM RFC for Delete Domain EPP Command][DKHMRFCDELETE]".

Domain names might be suspended for other reasons, these will no be recoverable using the described restore facility, this will be indicated using the `serverUpdateProhibited` status.

Restoration has to take place during the redemption period and will not be possible after the grace period has expired.

The restoration is requested using the update domain command.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<epp xmlns="urn:ietf:params:xml:ns:epp-1.0"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="urn:ietf:params:xml:ns:epp-1.0 epp-1.0.xsd">
    <command>
        <update>
            <domain:update xmlns:domain="urn:ietf:params:xml:ns:domain-1.0" xsi:schemaLocation="urn:ietf:params:xml:ns:domain-1.0 domain-1.0.xsd">
                <domain:name>example.com</domain:name>
                <domain:chg/>
            </domain:update>
        </update>
        <extension>
            <rgp:update xmlns:rgp="urn:ietf:params:xml:ns:rgp-1.0" xsi:schemaLocation="urn:ietf:params:xml:ns:rgp-1.0 rgp-1.0.xsd">
                <rgp:restore op="request"/>
            </rgp:update>
        </extension>
        <clTRID>ABC-12345</clTRID>
    </command>
</epp>
```

Example is lifted from [RFC:3915][RFC3915]

The interesting part is, the extension specifying the restore operation.

```xml
<rgp:update xmlns:rgp="urn:ietf:params:xml:ns:rgp-1.0" xsi:schemaLocation="urn:ietf:params:xml:ns:rgp-1.0 rgp-1.0.xsd">
    <rgp:restore op="request"/>
</rgp:update>
```

This will bring the domain name into the state of: `pendingRestore` for the restore, but the domain remains in: `pendingDelete`.

Next step is to acknowledge the restore operation using a report operation, which look as follows:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<epp xmlns="urn:ietf:params:xml:ns:epp-1.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="urn:ietf:params:xml:ns:epp-1.0
     epp-1.0.xsd">
    <command>
        <update>
            <domain:update xmlns:domain="urn:ietf:params:xml:ns:domain-1.0" xsi:schemaLocation="urn:ietf:params:xml:ns:domain-1.0 domain-1.0.xsd">
                <domain:name>example.com</domain:name>
                <domain:chg/>
            </domain:update>
        </update>
        <extension>
            <rgp:update xmlns:rgp="urn:ietf:params:xml:ns:rgp-1.0" xsi:schemaLocation="urn:ietf:params:xml:ns:rgp-1.0 rgp-1.0.xsd">
                <rgp:restore op="report">
                    <rgp:report>
                        <rgp:preData></rgp:preData>
                        <rgp:postData></rgp:postData>
                        <rgp:delTime>2003-07-10T22:00:00.0Z</rgp:delTime>
                        <rgp:resTime>2003-07-20T22:00:00.0Z</rgp:resTime>
                        <rgp:resReason></rgp:resReason>
                        <rgp:statement></rgp:statement>
                    </rgp:report>
                </rgp:restore>
            </rgp:update>
        </extension>
        <clTRID>ABC-12345</clTRID>
    </command>
</epp>
```

Example is lifted from [RFC:3915][RFC3915]

The proposal is to the the report part act as an acknowledgement. The domain name is restored _as-is_ if possible, so the mandatory fields:

- `rgp:preData`
- `rgp:postData`
- `rgp:resReason`
- `rgp:statement`

Have to be specified, but values are ignored. As are the optional field, which however is optional and does not have to be specified:

- `rgp:other`

The mandatory fields:

- `rgp:delTime`
- `rgp:resTime`

Have to be specified and will be evaluated according to [RFC:3915][RFC3915].

- The `rgp:delTime` value has to match the deletion date and time.
- The `rgp:resTime` value has to match date and time of the initial restore request (see above).

As described in [RFC:3915][RFC3915], multiple report requests can be submitted, until success and within the allowed timeframe of possible restoration.

The process described in [RFC:3915][RFC3915]. The resolution of the `rgp:resTime` might seem at bit to strict and perhaps this part of the validity check can be relaxed.

A response indicating unsuccessful restoration attempt will look as follows:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    <response>
        <result code="2004">
            <msg>Restore date is incorrect</msg>
        </result>
        <trID>
            <clTRID>ABC-12345</clTRID>
            <svTRID>54321-XYZ</svTRID>
        </trID>
    </response>
</epp>
```

Example lifted from [RFC:5730][RFC5730] and modified.

A response indicating successful restoration attempt will look as follows:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<epp xmlns="urn:ietf:params:xml:ns:epp-1.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="urn:ietf:params:xml:ns:epp-1.0
     epp-1.0.xsd">
    <response>
        <result code="1000">
            <msg lang="en">Command completed successfully</msg>
        </result>
        <extension>
            <rgp:upData xmlns:rgp="urn:ietf:params:xml:ns:rgp-1.0" xsi:schemaLocation="urn:ietf:params:xml:ns:rgp-1.0 rgp-1.0.xsd">
                <rgp:rgpStatus s="pendingRestore"/>
            </rgp:upData>
        </extension>
        <trID>
            <clTRID>ABC-12345</clTRID>
            <svTRID>54321-XYZ</svTRID>
        </trID>
    </response>
</epp>
```

Example is lifted from [RFC:3915][RFC3915].

The successful operation will result in a transaction deducing the restoration fee from the prepaid account. In addition to the restoration fee, the period fee is also deducted, extending the period for the domain name.

It is planned to extend the use of the extensions described in [RFC:3915][RFC3915], so the complete fee can both be inquired prior to restoration and is visible in an extended response to a successful operation. These proposed extensions will be described in futher detail at a later time, for now please refer to [RFC:3915][RFC3915] for details.

<a id="xsd-definition"></a>
## XSD Definition

The XSD definition supporting [RFC:3915][RFC3915], is included with [RFC:3915][RFC3915].

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

- [DK Hostmaster EPP Service Specification][DKHMEPPSPEC]
- [DK Hostmaster EPP Service XSD Repository][DKHMXSDSPEC]
- [RFC:3915 "Domain Registry Grace Period Mapping for the Extensible Provisioning Protocol (EPP)"][RFC3915]
- [RFC:5730 "Extensible Provisioning Protocol (EPP)"][RFC5730]
- ["DKHM RFC for Delete Domain EPP Command"][DKHMRFCDELETE]

[RFC5730]: https://www.rfc-editor.org/rfc/rfc5730.html
[RFC3915]: https://www.rfc-editor.org/rfc/rfc3915.html
[DKHMEPPSPEC]: https://github.com/DK-Hostmaster/epp-service-specification
[DKHMXSDSPEC]: https://github.com/DK-Hostmaster/epp-xsd-files
[CONCEPT]: https://www.dk-hostmaster.dk/en/new-basis-collaboration-between-registrars-and-dk-hostmaster
[DKHMXSD3.2]: https://github.com/DK-Hostmaster/epp-xsd-files/blob/master/dkhm-3.2.xsd
[DKHMRFCDELETE]: https://github.com/DK-Hostmaster/DKHM-RFC-Delete-Domain
[BALANCE]: https://www.verisign.com/assets/epp-sdk/verisign_epp-extension_balance_v01.html
