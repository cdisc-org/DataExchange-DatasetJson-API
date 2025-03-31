# Read-Only Dataset-JSON API

## Introduction

The main purpose of DataExchange-DatasetJson-API is to create a standard API specification for Dataset-JSON. Conformant 
Dataset-JSON servers may support the entire standard specification or a read-only instance. This document highlights
the read-only elements of the Dataset-JSON API.

The Dataset-JSON API specification has been implemented using the Open API Specification (OAS) v3.1. Read-only versions
of the API must implement endpoints that use the GET HTTP verb, but do not need to implement the other verbs such as POST,
PUT, PATCH, and DELETE. For example, some systems, such as an EDC system, may create an API to enable sponsors and CROs
to retrieve datasets, but may not support allowing those same users to create or alter datasets.

From a design perspective, the Dataset-JSON API focuses on the data exchange use case to make it simple to implement.
The read-only API provides a simple interface for clients to retrieve datasets from a server. In the future,  
an extended API may be developed that enables more dynamic methods for retrieving datasets based on the results of data
queries or filters.

## OAS Specification Documents

Use dataset-json-api-read-only.html to review the specification for the read-only API. This HTML page was generated 
from the OAS specification in JSON.

Use the dataset-json-api-read-only.json file, a machine-readable specification, to generate software that works with 
the API, such as an API client. The HTML version of the specification was generated from the JSON version.

## Usage Examples

The examples below demonstrate how to use a read-only Dataset-JSON API implementation. The examples run against a 
proof-of-concept implementation. 

If you're interested in testing a different API implementation, replace the host (and port) to match the API 
implementation.

| Description                                                                        | Example Request                                                                                                                                                                                                                                                                                                                                        |
|------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| GET the about resource                                                             | curl https://dsjapi.net/about                                                                                                                                                                                                                                                                                                                          |
| GET the current list of studies                                                    | curl https://dsjapi.net/studies                                                                                                                                                                                                                                                                                                                        |
| GET a specific study                                                               | curl https://dsjapi.net/studies/cdisc.com-CDISCPILOT01                                                                                                                                                                                                                                                                                                 |
| GET the list of datasets for a specified study                                     | curl https://dsjapi.net/studies/cdisc.com-CDISCPILOT01/datasets                                                                                                                                                                                                                                                                                        |
| GET the list of datasets for a specified study and filter by datetime              | curl https://dsjapi.net/studies/cdisc.com-CDISCPILOT01/datasets -H "IF-Modified-Since: 2024-11-11T15:09:17"                                                                                                                                                                                                                                            |
| GET the list of datasets with a standard filter of sdtmig                          | curl https://dsjapi.net/studies/cdisc.com-CDISCPILOT01/datasets?standard=sdtmig                                                                                                                                                                                                                                                                        |
| GET the list of datasets for a specified study and filter by datetime and standard | curl https://dsjapi.net/studies/cdisc.com-CDISCPILOT01/datasets?standard=sdtmig -H "IF-Modified-Since: 2024-11-11T15:09:17"                                                                                                                                                                                                                            |
| GET the IG.AE dataset                                                              | curl https://dsjapi.net/studies/cdisc.com-CDISCPILOT01/datasets/IG.AE                                                                                                                                                                                                                                                                                  |
| GET the IG.AE dataset with IF-Modified-Since same as creationDateTime              | curl https://dsjapi.net/studies/cdisc.com-CDISCPILOT01/datasets/IG.AE -i -H "IF-Modified-Since: 2024-11-11T15:09:13"                                                                                                                                                                                                                                   |
| GET gzipped content (for datasets with 50 or more rows)                            | curl https://dsjapi.net/studies/cdisc.com-CDISCPILOT01/datasets/IG.AE -H "Accept-Encoding: gzip, deflate"                                                                                                                                                                                                                                              |
| GET dataset metadata only                                                          | curl https://dsjapi.net/studies/cdisc.com-CDISCPILOT01/datasets/IG.AE?metadataonly=True                                                                                                                                                                                                                                                                |
| GET page of data using offset and limit                                            | curl "https://dsjapi.net/studies/cdisc.com-CDISCPILOT01/datasets/IG.AE?offset=10&limit=40"                                                                                                                                                                                                                                                             |

## Assumptions

* If no datasets that match the criteria are found, a status code of 200 with content {'datasets': []} is returned
* When using the IF-Modified-Since header, a 304 status code will be returned if the dataset has not been modified since the date provided
* If a specific dataset is requested but not found a 404 status code is returned
* The standard filter is not case-sensitive

## Limitations

The Dataset-JSON API implementation at dsjapi.net is a proof-of-concept implementation developed for the purposes of
exploring and testing the Dataset-JSON API standard. The API is not a production ready application and is running on a
small server.

## Full CRUD API

The Dataset-JSON API supports full Create, Read, Update, and Delete (CRUD) operations for those implementations where 
clients can modify the datasets. 

In the proof-of-concept implementation, the read-only API, and GET requests in general, do not require an API Key. 
For CRUD operations other than read, an API Key is required.

Production servers may require an API Key for all operations.

## TODO

New feature requests that came out of a Data Exchange Team meeting:
- Add a context or environment to represent different versions of the data, such as UAT vs. Production
- In addition to context or environment, there may be a need to represent dataset versions
- Consider an optional endpoint for Define
- Consider prioritizing NDJSON over JSON

