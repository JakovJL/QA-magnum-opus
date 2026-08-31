# SOAP and SoapUI

## Table of Contents

- [[#What Is SOAP]]
- [[#SOAP Message Structure]]
- [[#WSDL and Contract]]
- [[#SOAP and REST]]
- [[#Working in SoapUI]]
- [[#What to Check in a SOAP API]]
- [[#Common Mistakes]]
- [[#Key Takeaways]]

**Related notes:** [[AQA Java eng]]

---

## What Is SOAP

**SOAP** is a protocol for exchanging messages between systems. It usually runs over HTTP, but it can also work over other transport protocols. Data is transferred in XML.

**Goal:** give systems a strict, well-described contract for interaction.

SOAP is common in banking, government and corporate systems, where strict rules, security and compatibility matter.

---

## SOAP Message Structure

A SOAP request consists of an `Envelope`, which contains:

- `Header` — optional service data: authentication, request ID, security settings;
- `Body` — the required request or response data;
- `Fault` — the error description, if the server could not process the request.

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <GetUserRequest>
      <id>42</id>
    </GetUserRequest>
  </soap:Body>
</soap:Envelope>
```

> [!caution] XML is sensitive to structure
> A wrong element name, namespace or nesting level can make the request invalid, even if the data looks correct.

---

## WSDL and Contract

**WSDL** is an XML file that describes a SOAP service: the available operations, input and output messages, the service address and the rules of exchange.

From the WSDL you can learn:

- which operations the service supports;
- which fields are required;
- which data types are expected;
- what the response and the error look like.

For a tester, the WSDL is the main source of the expected contract. It does not replace business requirements, but it helps to check technical correctness.

---

## SOAP and REST

> [!tip] Quick comparison
>
> | Criterion | SOAP | REST |
> |---|---|---|
> | Basis | Protocol | Architectural style |
> | Data format | Usually XML | Often JSON, XML is possible |
> | Contract | Usually WSDL | Can be OpenAPI |
> | Errors | SOAP Fault | HTTP status code and response body |
> | Typical case | Strict corporate integrations | Web and mobile APIs |

---

## Working in SoapUI

**SoapUI** is a tool for manual testing of SOAP APIs. It can import a WSDL and create ready-made request templates.

A typical workflow:

1. Create a SOAP project and specify the WSDL URL.
2. Select the operation you need.
3. Fill in the values in the generated XML.
4. Send the request and study the response.
5. Add checks: status, an XPath expression, response time or the content of the `Fault`.

For repeated scenarios you can use TestSuite and TestCase. Keep test data separate from the request itself.

---

## What to Check in a SOAP API

- a successful response and the expected data in the `Body`;
- required and optional fields;
- wrong types, empty and boundary values;
- a correct `SOAP Fault` for an error;
- authentication and access rights;
- whether the response matches the WSDL and the business requirements;
- the side effect: was a record created, did a status change, did an event fire.

---

## Common Mistakes

| Mistake | What to do better |
|---|---|
| Check only HTTP 200 | Check the `Body` content and the result of the operation |
| Ignore the `Fault` | Check the error code and how clear the message is |
| Edit the XML by hand without checking the structure | Use the template from the WSDL and validate the XML |
| Treat the WSDL as a full description of the behaviour | Compare the contract with the business requirements |

---

## Key Takeaways

> [!summary] Key points in 30 seconds
> - SOAP is a message-exchange protocol (usually over HTTP); data is in XML; a strict contract — typical for banking and corporate systems
> - Message structure: Envelope → Header (service data) + Body (request or response data) + Fault (error description)
> - The WSDL describes the service: operations, required fields, types, address; for a tester it is the main source of the expected contract
> - XML is sensitive to structure: a wrong element name or namespace makes the request invalid, even if the data is correct
> - SOAP is a protocol, REST is an architectural style; SOAP errors come as a Fault, REST errors as HTTP status codes
> - SoapUI imports a WSDL and generates request templates; for repeated scenarios use TestSuite and TestCase
> - Check more than HTTP 200: the `Body` content, a correct `Fault` and side effects (a record created, a status changed)
