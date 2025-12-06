# Dataset-JSON REST API Version 1.0 User Guide

| Date         | Version | Summary of Changes |
|--------------|---------|--------------------|
| 2025-12-11   | 1.0     | Final              |

- [Introduction](#introduction)
- [OpenAPI Specification](#openapispecification)
- [API Implementations](#apiimplementations)
- [REST API](#restapi)
- [HTTP verbs](#httpverbs)
- [JSON and NDJSON](#jsonandndjson)
- [Usage](#usage)
- [Usage Examples](#usageexamples)
  - [Read-only API Examples](#readonlyapiexamples)
  - [Write, Update, Append, Delete API Examples](#writeupdateappenddeleteapiexamples)
- [Optional Endpoints and Features](#optionalendpointsandfeatures)
  - [Adding Support for Multiple Environments](#addingsupportformultipleenvironments)
  - [Dataset Versions](#datasetversions)
  - [Retrieving the Define-XML](#retrievingthedefinexml)
  - [Streaming NDJSON Datasets](#streamingndjson)
- [API Identifiers](#apiidentifiers)
- [HATEOAS](#hateoas)
- [Metadata and Data Only Flags](#metadataanddataonlyflags)
- [HTTP Status Codes](#httpstatuscodes)
- [Implementation Notes](#implementationnotes)
- [Assumptions](#assumptions)
- [References](#references)
- [Glossary and Abbreviations](#glossaryandabbreviations)

## <a id="introduction"></a>Introduction

This User Guide (UG) complements the Dataset-JSON API Specification published as HTML and JSON in the
[Dataset-JSON API GitHub repository](https://github.com/cdisc-org/DataExchange-DatasetJson-API). This UG provides
additional information to aid those implementing the Dataset-JSON API.

The main purpose of the Dataset-JSON API is to create a standard API specification for Dataset-JSON. Conformant
Dataset-JSON servers may support the entire standard specification or a read-only instance.

The standard REST API specification is based on the Dataset-JSON Version 1.1 specification found in [Dataset-JSON
GitHub repository](https://github.com/cdisc-org/DataExchange-DatasetJson).

## <a id="openapispecification"></a>OpenAPI Specification

The primary technical documentation for the Dataset-JSON API is found in the
[OpenAPI Specification 3.1 (OAS) for the Dataset-JSON API](https://github.com/cdisc-org/DataExchange-DatasetJson-API/blob/main/openapi/dataset-json-api-1-0.json).
An [HTML version of the specification](https://html-preview.github.io/?url=https://github.com/cdisc-org/DataExchange-DatasetJson-API/blob/main/docs/dataset-json-api-1-0.html)
has been generated as a human-readable representation. The machine-readable JSON version of the OAS Dataset-JSON API
specification includes a machine-readable representation of the endpoints and JSON payloads that may be used by code
generators to create basic client and server software.

## <a id="apiimplementations"></a>API Implementations

The specification focuses on data exchange to keep the API implementation simple. Implementers may seek to extend the API
to add additional features, such as filtering datasets for specific rows that meet a given criteria, but these
advanced features are not part of the standard. Ease of implementation was a key design principle for the API specification.

For those implementing the API, there are 2 paths to conformance with the standard.

1. Implement a read-only API by implementing the GET verbs.
2. Implement a CRUD API that allows clients to create, read, update, delete, and append to datasets.

A read-only API implementation implements all the GET verbs, and not the other verbs. For example, an
EDC system implementing the API may allow clients to retrieve datasets, but not to create or update datasets.

Systems must implement all required elements of the specification to be conformant. For a read-only implementation, this means
implementing each GET request completely to include all required parameters and features. CRUD implementations must
implement all verbs with all the required parameters and features documented in the specification.

When implementing an API server, the JSON objects documented in the API specification may be extended to include
additional fields to address server-only requirements, such as the name and location of the dataset on the server. 
These additional fields will vary depending on how the server stores and retrieves the data. For example,
a server may store the datasets on a file server or maintain the study data used to generate the datasets in a database.

## <a id="restapi"></a>REST API

The Dataset-JSON API is a REST-based API specification. REST, or
[Representational State Transfer](https://ics.uci.edu/~fielding/pubs/dissertation/top.htm), is an architectural style for
distributed hypermedia systems first presented in Roy Fielding's 2000 dissertation[1]. REST is the most widely implemented
architecture for building web-based APIs.  

## <a id="httpverbs"></a>HTTP Verbs

- GET is used to retrieve content such as a list of studies, a study, a list of datasets, or a dataset
- POST is used to create a new study or dataset
- PUT is used to update a study or dataset
- PATCH is used to append data rows to a dataset
- DELETE is used to soft delete a study or dataset

## <a id="jsonandndjson"></a>JSON and NDJSON

The Dataset-JSON API returns and accepts the JSON representation of Dataset-JSON. The NDJSON format, also part
by the Dataset-JSON standard, is supported as an optional export format where the dataset content can be streamed to a 
client. 

## <a id="usage"></a>Usage

In this User Guide, example endpoints will use a local IP address:

```
http://127.0.0.1:8000/
```

To load the API documentation generated by the OAS specification:

```
http://127.0.0.1:8000/docs#/
```

Note: For Dataset-JSON API implementations, you will need to replace the host name and port to use your own URL.
For example, http://127.0.0.1:8000/ becomes:

```
https://dsjapi.net/
```

## <a id="usageexamples"></a>Usage Examples

In the following examples, replace the host and port listed to match your API implementation. The api-key used in the
examples must be replaced with a valid key.

### <a id="readonlyapiexamples"></a>Read-only API Examples

|Description                                                                        | Description                                                                                                                                                             |
|--------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| GET the about resource                                                   | curl http://127.0.0.1:8000/about -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"                                                                                     |
| GET the home resource (html page)                                        | curl http://127.0.0.1:8000/ -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"                                                                                          |
| GET the current list of studies                                          | curl http://127.0.0.1:8000/studies -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"                                                                                   |
| GET a specific study                                                     | curl http://127.0.0.1:8000/studies/CDISCPILOT01 -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"                                                                      |
| GET the list of datasets for a specified study                           | curl http://127.0.0.1:8000/studies/CDISCPILOT02/datasets -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"                                                             |
| GET the list of datasets for a specified study and filter by datetime    | curl http://127.0.0.1:8000/studies/CDISCPILOT02/datasets -H "IF-Modified-Since: 2024-01-10T20:38:47" -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"                 |
| GET the list of datasets with a standard filter of sdtmig                | curl http://127.0.0.1:8000/studies/CDISCPILOT02/datasets?standard=sdtmig -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"                                             |
| GET the list of datasets for a study and filter by datetime and standard | curl http://127.0.0.1:8000/studies/CDISCPILOT02/datasets?standard=sdtmig -H "IF-Modified-Since: 2024-01-10T20:38:47" -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a" |
| GET the IG.AE dataset                                                    | curl http://127.0.0.1:8000/studies/CDISCPILOT01/datasets/IG.AE -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"                                                       |
| GET the IG.AE dataset with IF-Modified-Since same as creationDateTime    | curl http://127.0.0.1:8000/studies/CDISCPILOT01/datasets/IG.AE -i -H "IF-Modified-Since: 2024-01-10T20:38:47" -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"        |
| GET gzipped content                                                      | curl http://127.0.0.1:8000/studies/CDISCPILOT02/datasets/IG.DD?standard=sdtmig -H "Accept-Encoding: gzip, deflate" -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"   |
| GET dataset metadata only                                                | curl http://127.0.0.1:8000/studies/CDISCPILOT01/datasets/IG.AE?metadataonly=True -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"                                     |
| GET page of data using offset and limit                                  | curl http://127.0.0.1:8000/studies/CDISCPILOT01/datasets/IG.AE?offset=10&limit=40 -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"                                    |

### <a id="writeupdateappenddeleteapiexamples"></a>Write, Update, Append, Delete API Examples

| Description                                                  | Description                                                                                                                                                                                                                                                                                                                                               |
|--------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| POST a new study                                             | curl http://127.0.0.1:8000/studies -X POST -H "Content-Type: application/json" -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a" -d  "{\"oid\": \"CDISCPILOT03\", \"name\": \"CDISCPILOT03\", \"label\": \"Study Data Tabulation Model Metadata Submission Guidelines Sample Study\", \"standards\": [\"sdtmig\"], \"href\": \"/studies/CDISCPILOT03\"}" |
| PUT update the newly added study                             | curl http://127.0.0.1:8000/studies/CDISCPILOT03 -X PUT -H "Content-Type: application/json" -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a" -d  "{\"oid\": \"CDISCPILOT03\", \"name\": \"CDISCPILOT03\", \"label\": \"SDTM MSG Sample Study\", \"standards\": [\"sdtmig\"], \"href\": \"/studies/CDISCPILOT03\"}"                                       |
| POST add a new dataset to the new study                      | curl http://127.0.0.1:8000/studies/CDISCPILOT03/datasets -X POST -H "Content-Type: application/json" -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a" -d  @.\test-data\ig-dd.json                                                                                                                                                                       |
| PUT update the newly added dataset                           | curl http://127.0.0.1:8000/studies/CDISCPILOT03/datasets/IG.DD -X PUT -H "Content-Type: application/json" -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a" -d  @.\test-data\ig-dd-put.json                                                                                                                                                              |
| PATCH append a record to the newly added dataset             | curl http://127.0.0.1:8000/studies/CDISCPILOT03/datasets/IG.DD -X PATCH -H "Content-Type: application/json" -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a" -d @.\test-data\ig-dd-patch.json                                                                                                                                                           |
| POST add new dataset to the new study using Gzip compression | curl http://127.0.0.1:8000/studies/CDISCPILOT03/datasets -X POST -H "Content-Encoding: application/gzip" -H "Content-Type: application/json" -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a" --data-binary @.\test-data\ig-ta.gz                                                                                                                       |
| DELETE the IG.TA dataset                                     | curl http://127.0.0.1:8000/studies/CDISCPILOT03/datasets/IG.TA -X DELETE -I -H "api-key: x1y62c5b-e29d-8fa5-af31-63c2be65681a"                                                                                                                                                                                                                            |

## <a id="optionalendpointsandfeatures"></a>Optional Endpoints and Features

At a minimum, each Dataset-JSON API instance should implement a read-only API. This means implementing the GET verb for
all required endpoints. A full CRUD API is optional.

A few API features are optional, and a server does not need to support them to be conformant.

- Multiple environments
- Dataset versions
- Define-XML retrieval
- NDJSON dataset streaming

If a server does not implement an optional endpoint, it should return a 501 status code to indicate to the client that
the feature is not available.

### <a id="addingsupportformultipleenvironments"></a>Adding Support for Multiple Environments

When using the API with multiple environments, such as QA and Production, use a different base URL for each environment.
Building on our previous example where the base URL is http://127.0.0.1:8000/, create different base URLs to support
different environments, for example:

- http://127.0.0.1:8000/qa
- http://127.0.0.1:8000/uat
- http://127.0.0.1:8000/prod

Each environment serves datasets that are undergoing QA, UAT, or Production. Not all implementers will opt to use
multiple environments, so this strategy only applies to those interested in having the API serve datasets from different
environments.

### <a id="datasetversions"></a>Dataset Versions

By default, the Dataset-JSON API maintains only the most recent version of each dataset within a study. However,
the API optionally supports a **snapshot** mechanism that creates a versioned state of a study’s
datasets. A snapshot captures and preserves the state of all datasets in a study at a specific point in time,
allowing clients to reference or retrieve historical versions for purposes such as auditing, comparison, or archival.

Snapshots are created via the `snapshot` endpoint using the HTTP `POST` method. The client may provide an optional
unique `label` to identify the snapshot. For example:

```
POST /studies/{studyOID}/snapshots?label=draft
```

When a snapshot is created, the API generates an immutable copy of the study and all associated datasets as they
exist at the moment of the request. The API generates a unique snapshotID for each snapshot created. The specification
does not prescribe how the API generates the snapshotID. Many version control systems use a unique hash value generated 
using an algorithm, such as SHA-256, as the unique ID. The specification does not limit the number of snapshots that may
be created, though API implementations may impose such limits.

To get a list of all the available snapshots for a given study, use the following endpoint:

```
GET /studies/{studyOID}/snapshots
```

To retrieve a specific snapshot, the client issues a `GET` request to the snapshot endpoint with the snapshotID
identifier:
```
GET /studies/{studyOID}/snapshots/{snapshotID}
```

Example URL for retrieving specific snapshot versions:
```
GET http://127.0.0.1:8000/studies/CDISCPILOT01/snapshots/2cf24db
```

This request returns the study metadata and a list of datasets as they were at the time the snapshot was created.

To get a specific snapshot dataset, use a URL from the list of datasets returned using the above endpoint. The snapshot
dataset URLs use the following format:

```
GET /studies/{studyOID}/snapshots/{snapshotID}/datasets/{datasetOID}
```

Snapshot labels are client-defined and make it easier for a user to select a specific snapshot version. The specification
considers the snapshot label optional, but client software may opt to require it. When provided, a label must be 
**unique within the context of a study**. Labels may be any valid UTF-8 string. Examples include:

```
"dry run"
"draft"
"final"
"dmc"
```

> **Note:** Support for snapshot/versioning functionality is optional. API implementations are not required to support this feature to be conformant with the Dataset-JSON API standard.

### <a id="retrievingthedefinexml"></a>Retrieving the Define-XML

The Study resource may include a metaDataRef attribute, which contains one or more URIs to the study’s Define-XML metadata.
A study can have multiple metaDataRef URIs. For example, a study may include separate Define-XML files for the SDTM and 
ADaM standards.

To retrieve all Define-XML files associated with a study:

```
GET /studies/{studyOID}/defines
```

The response returns an array of objects, each containing:

- label: a unique identifier for the Define-XML (commonly the standard, e.g., sdtmig or adamig).
- href: a URL to retrieve the Define-XML file.

To retrieve a single Define-XML file:

```
GET /studies/{studyOID}/defines/{label}
```

The {label} path parameter corresponds to the label provided in the list response. The `label` must be unique within 
the study.

To add a Define-XML to a study:

```
POST /studies/{studyOID}/defines
```

The request body for posting a new Define-XML:

- label: identifies the standard or purpose of the Define-XML.
- defineXML: contains the full XML content of the Define-XML.

```
{
  "label": "sdtmig",
  "defineXML": "<ODM> ... </ODM>"
}
```

> **Note:** Implementing the `metaDataRef` attribute is optional. It is not required to conform with the Dataset-JSON API standard.
Not all data providers that implement a Dataset-JSON API will produce the Define-XML metadata.

### <a id="streamingndjson"></a>Streaming NDJSON Datasets

NDJSON datasets may be exported using the API and retrieved via streaming. This provides an efficient way to retrieve 
large datasets. NDJSON API support is optional.

The following endpoint initiates a dataset export: 
```
GET /studies/{studyOID}/datasets/{datasetOID}/$export
```
Many servers will implement this as an asynchronous operation. In this case, the server returns a 202 status code
indicating that the export request has been successfully accepted for processing, but processing may not be completed.
The server returns content that includes the URL for retrieving the exported dataset as a stream. The following 
endpoint allows the client to begin streaming the exported NDJSON dataset:
```
GET /studies/{studyOID}/datasets/{datasetOID}/ndjson
```
If the server receives the above request prior to completing the asynchronous export process, the server returns
a 202 Accepted status code to inform the client that the request was successfully received, but processing has not
completed.

The streamed NDJSON dataset uses the media type `application/x-ndjson`.

> **Note:** Implementing NDJSON support in the API is optional. It is not required to conform with the Dataset-JSON API standard. 

## <a id="apiidentifiers"></a>API Identifiers

The Dataset-JSON API uses the `StudyOID` and `ItemGroupOID` as identifiers in the API. For example, to request a specific
dataset the URL must contain the `StudyOID` and the `ItemGroupOID` to indicate the dataset of interest. The following is
an example URL requesting the AE dataset for the CDISCPILOT01 study.

```
http://127.0.0.1:8000/studies/CDISCPILOT01/datasets/IG.AE
```

In this example, the `StudyOID` is CDISCPILOT01 and the `ItemGroupOID` is IG.AE. Since these OIDs, also used in an
associated Define-XML file, are used in the URL, avoid using certain characters in the OIDs to better support usage in
a URL, such as:

- '#' (fragment identifier)
- '?' (query string delimiter)
- '&' (query string parameter separator)
- '=' (assigns values to query string parameters)
- '+' (represents a space in query strings)
- '%' (used for URL encoding)
- '/' (path separator)

## <a id="hateoas"></a>HATEOAS

HATEOAS, or Hypertext as the Engine of Application State, means that a REST API must provide hypermedia links in its
responses, guiding clients through available actions and resources. The Dataset-JSON API does provide hyperlinks to inform
clients of the available study and dataset resources. The API specification does not include a `_links` section to represent
the hyperlinks, but the relevant URLs are available in the body of the response. Many REST APIs, such as the
CDISC Library, include a `_links` section, but it was not included in the Dataset-JSON API specification to simplify the
API implementation. To support HATEOAS, the URLs are available in the body of the responses.

## <a id="metadataanddataonlyflags"></a>Metadata and Data-only Flags

Clients may request to receive only the dataset metadata or only the dataset data. By default, responses include the
metadata and data parts of the Dataset-JSON dataset. Depending on the usage scenario, it may be preferable to only
receive the metadata or the data, and `metadataonly=True` or `dataonly=True` query parameters make that possible. The entire
dataset is returned by default.

## <a id="httpstatuscodes"></a>HTTP Status Codes

| Status Code | Definition            | Description                                                                                                 |
|-------------|-----------------------|-------------------------------------------------------------------------------------------------------------|
| 200         | OK                    | The request was successfully processed                                                                      |
| 201         | Successfully created  | Indicates that the request was successful and a new resource has been created as a result                   |
| 202         | Accepted              | The request has been successfully accepted for processing, but processing may not be complete               |
| 204         | No content            | The server successfully processed a request but no content is returned in the response body                 |
| 304         | Not modified          | The client's cached version of a resource is still valid and should be used instead of re-downloading it    |
| 400         | Bad request           | The server couldn't process the request due to a client-side issue, usually an invalid or malformed request |
| 401         | Unauthorized          | The server requires authentication to grant access to the requested resource                                |
| 404         | Not Found             | The server could not locate the requested resource                                                          |
| 409         | Conflict              | The server cannot complete the request due to a conflict with the current state of the resource             |
| 413         | Content too large     | Unable to process the request due to the content being too large to process                                 |
| 422         | Unprocessable entity  | The server cannot complete the request due to a conflict with the current state of the resource             |
| 500         | Internal server error | The server encountered an unexpected condition and couldn't complete the request                            |
| 501         | Not implemented       | For optional features that are not supported by an implementation                                           |

## <a id="implementationnotes"></a>Implementation Notes

- If no datasets that match user specified criteria are found, a status code of 200 with content {'datasets': []} is returned.
- If a specific dataset is requested and not found, a 404 status code is returned.
- The standard filter is not case-sensitive so that `standard=sdtmig` and `standard=SDTMIG` are both valid query parameters.
- The standard filters include: "sdtmig", "sendig", "adamig", and "other".
- Gzip compression should be supported by API implementation. Alternative standard compression algorithms may also be used, such as Brotli or Zstandard.
- Gzip uses the DEFLATE algorithm for compression by default, the same algorithm as the Compressed Dataset-JSON standard. 
- After a dataset has been posted (created) the API returns the Study Dataset metadata, but not the full dataset that was posted.
- When creating a new study, only the studyOID, name, label, and href are required. The standards attribute is optional.
- After creating a new study, add datasets, snapshots, and defines using the appropriate endpoints.
- Full list of available HTTP status codes: https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml.
- When posting a study or dataset that already exists, a 409 status code will be returned.
- When requesting a dataset too large for the server to process, a 413 status code will be returned and the client may opt to re-send the request for a chunk of records instead of the entire dataset.
- When posting a dataset to a study that does not exist, a 422 status code will be returned.
- The Dataset-JSON API works with JSON, but can export and stream NDJSON datasets.

## <a id="assumptions"></a>Assumptions

- The API standard focuses on data exchange over other use cases such as querying or creating slices, which may be added to an extended version of the API.
- A read-only API implementation is valid.
- In practice, an API implementation will not maintain so many active studies that a filter is needed on the list of studies returned.
- Most dataset retrievals will use gzip compression.
- Many APIs will never read or write to a Dataset-JSON file, but instead will read and write to a data store and only use Dataset-JSON to send and receive data from API clients.
- The current API specification supports Dataset-JSON v1.1.

## <a id="references"></a>References
1. Fielding RT. Architectural Styles and the Design of Network-based Software Architectures. Doctoral dissertation, University of California, Irvine, 2000.

## <a id="glossaryandabbreviations"></a>Glossary and Abbreviations
The following table lists some of the abbreviations and terms used in this document.
<table title="Glossary and Abbreviations">
  <tbody>
    <tr>
      <td>ADaM</td>
      <td>
        Analysis Dataset Model. CDISC Foundational standard for modeling
        data: <a href=
        "https://www.cdisc.org/standards/foundational/adam">https://www.cdisc.org/standards/foundational/adam</a>
      </td>
    </tr>
    <tr>
      <td>API</td>
      <td>
        Application Programming Interface
      </td>
    </tr>
    <tr>
      <td>CDISC</td>
      <td>
        Clinical Data Interchange Standards Consortium
      </td>
    </tr>
    <tr>
      <td>CRUD</td>
      <td>
        Create Read Update Delete
      </td>
    </tr>
    <tr>
      <td>Define-XML</td>
      <td>
        CDISC Data Exchange standard for sharing metadata: <a href=
        "https://www.cdisc.org/standards/data-exchange/define-xml">https://www.cdisc.org/standards/data-exchange/define-xml</a>
      </td>
    </tr>
    <tr>
      <td>EDC</td>
      <td>
        Electronic Data Capture
      </td>
    </tr>
    <tr>
      <td>HATEOAS</td>
      <td>
        Hypertext as the Engine of Application State
      </td>
    </tr>
    <tr>
      <td>JSON</td>
      <td>
        JavaScript Object Notation
      </td>
    </tr>
    <tr>
      <td>NDJSON</td>
      <td>
        Newline Delimited JSON
      </td>
    </tr>
    <tr>
      <td>OAS</td>
      <td>
        OpenAPI Specification
      </td>
    </tr>
    <tr>
      <td>OID</td>
      <td>
        Object Identifier
      </td>
    </tr>
    <tr>
      <td>REST</td>
      <td>
        Representational State Transfer
      </td>
    </tr>
    <tr>
      <td>SDTM</td>
      <td>
        Study Data Tabulation Model. CDISC Foundational standard for
        modeling data: <a href=
        "https://www.cdisc.org/standards/foundational/sdtm">https://www.cdisc.org/standards/foundational/sdtm</a>
      </td>
    </tr>
    <tr>
      <td>UAT</td>
      <td>
        User Acceptance Testing
      </td>
    </tr>
    <tr>
      <td>UG</td>
      <td>
        User Guide
      </td>
    </tr>
    <tr>
      <td>URI</td>
      <td>
        Uniform Resource Identifier
      </td>
    </tr>
    <tr>
      <td>URL</td>
      <td>
        Uniform Resource Locator
      </td>
    </tr>
    <tr>
      <td>UTF</td>
      <td>
         Unicode Transformation Format
      </td>
    </tr>
  </tbody>
</table>
