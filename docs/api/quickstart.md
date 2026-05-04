---
kernelspec:
  name: python3
  display_name: Python 3
---

# Quick start

:::{admonition} Time estimate: 5 minutes
:class: tip
This tutorial walks you through your first requests to the OpenCitations API. You'll start by counting citations for a paper, then retrieve the full citation data, and finally look up bibliographic metadata.

No account, no API key, no setup required.
:::

## What is the OpenCitations API?

OpenCitations collects and publishes open citation data from scholarly literature. All this data is accessible through an API (Application Programming Interface), which is a way for programs and scripts to retrieve information directly from OpenCitations by requesting a specific URL.

You send a request, and you get back structured data with the answer. This means you can use it in your scripts, your analysis pipelines, or even embed results into your own website.

OpenCitations provides two APIs:

- **Index API** - citation data: who cites whom, when, and how they relate
- **Meta API** - bibliographic metadata: titles, authors, venues, dates

In this tutorial you will use both, starting from a single DOI.

## Before we start: identifiers

The API identifies scholarly works through persistent identifiers (PIDs). You pass them in the URL using the format `prefix:value`.

| Prefix | Name | Example |
|--------|------|---------|
| `doi` | Digital Object Identifier | `doi:10.1162/qss_a_00023` |
| `pmid` | PubMed Identifier | `pmid:33817056` |
| `omid` | OpenCitations Meta Identifier | `omid:br/062501777134` |

The most common case is having a DOI. If you have one, you're good.

## Step 1: Count citations for a paper

One of the things you can do with the API is count how many times a paper has been cited. To do this, you just need to open a specific URL that contains the DOI of the paper you are interested in.

Throughout this tutorial, we'll use the DOI `10.1162/qss_a_00023` as an example.

The simplest way to try the API is to open this URL in your browser:

```
https://api.opencitations.net/index/v2/citation-count/doi:10.1162/qss_a_00023
```

You will see the response directly in the browser window:

```{code-cell} python
:tags: [remove-input]
import requests
import json

response = requests.get(
    "https://api.opencitations.net/index/v2/citation-count/doi:10.1162/qss_a_00023"
)
print(json.dumps(response.json(), indent=4))
```

You just made your first API call. You opened a URL, and OpenCitations responded with data in JSON format. Every interaction with the API works the same way: a URL in, structured data out.

Now let's do the same thing from the command line and from Python, which is how you'll use the API in practice:

::::{tab-set}
:sync-group: code

:::{tab-item} curl
:sync: curl
```bash
curl 'https://api.opencitations.net/index/v2/citation-count/doi:10.1162/qss_a_00023'
```
:::

:::{tab-item} Python
:sync: python
```python
import requests

response = requests.get(
    "https://api.opencitations.net/index/v2/citation-count/doi:10.1162/qss_a_00023"
)
result = response.json()
print(result)
```
:::

::::

| Field | Meaning |
|-------|---------|
| `count` | Number of works that cite the queried paper |

:::{admonition} Try it yourself
:class: note
Replace the DOI in the URL with any DOI you care about. The pattern is always `/index/v2/citation-count/doi:<your-doi>`.

The endpoint also accepts `pmid:` and `omid:` prefixes.
:::

## Step 2: Get the full list of citations

A count is useful, but often you need to know *which* works cited the paper. The `citations` endpoint returns the complete list with details about each citation.

::::{tab-set}
:sync-group: code

:::{tab-item} curl
:sync: curl
```bash
curl 'https://api.opencitations.net/index/v2/citations/doi:10.1162/qss_a_00023'
```
:::

:::{tab-item} Python
:sync: python
```python
response = requests.get(
    "https://api.opencitations.net/index/v2/citations/doi:10.1162/qss_a_00023"
)
citations = response.json()

for cit in citations[:3]:
    print(f"Cited by: {cit['citing']}")
    print(f"  Date: {cit['creation']}")
    print()
```
:::

::::

The response is a JSON array. Each element is one citation:

```json
{
    "oci": "0605028322-062501777134",
    "citing": "omid:br/0605028322 doi:10.1007/s11192-025-05248-8",
    "cited": "omid:br/062501777134 doi:10.1162/qss_a_00023 openalex:W3106215946",
    "creation": "2025-02-14",
    "timespan": "P5Y0M",
    "journal_sc": "no",
    "author_sc": "no"
}
```

| Field | Meaning | Example |
|-------|---------|---------|
| `oci` | Open Citation Identifier, a unique ID for this specific citation | `0605028322-062501777134` |
| `citing` | All known identifiers of the citing work (the one containing the reference) | `omid:br/0605028322 doi:10.1007/s11192-025-05248-8` |
| `cited` | All known identifiers of the cited work (the one being referenced) | `omid:br/062501777134 doi:10.1162/qss_a_00023 openalex:W3106215946` |
| `creation` | Publication date of the citing work, in ISO 8601 format | `2025-02-14` |
| `timespan` | Time between the publication of the cited and the citing work, in [XSD duration format](https://www.w3.org/TR/xmlschema11-2/#duration) | `P5Y0M` = 5 years, 0 months |
| `journal_sc` | Whether both works were published in the same journal (journal self-citation) | `no` |
| `author_sc` | Whether the two works share at least one author (author self-citation) | `no` |

The `citing` and `cited` fields contain multiple identifiers separated by spaces. OpenCitations stores all the known IDs for a given work: its internal OMID, the DOI, the OpenAlex ID, etc.

:::{admonition} What is an OCI?
:class: info
The **Open Citation Identifier** (OCI) is a persistent identifier that OpenCitations assigns to each citation. It is composed of two numbers separated by a dash: the first refers to the citing work, the second to the cited work. More details in [What are OCIs](concepts/oci.md).
:::

## Step 3: Retrieve bibliographic metadata

The Index API tells you *who cites whom*, but not the titles or authors. That information lives in the **Meta API**.

::::{tab-set}
:sync-group: code

:::{tab-item} curl
:sync: curl
```bash
curl 'https://api.opencitations.net/meta/v1/metadata/doi:10.1162/qss_a_00023'
```
:::

:::{tab-item} Python
:sync: python
```python
response = requests.get(
    "https://api.opencitations.net/meta/v1/metadata/doi:10.1162/qss_a_00023"
)
metadata = response.json()[0]

print(f"Title:     {metadata['title']}")
print(f"Authors:   {metadata['author']}")
print(f"Published: {metadata['pub_date']}")
print(f"Venue:     {metadata['venue']}")
```
:::

::::

Response:

```json
{
    "id": "doi:10.1162/qss_a_00023 openalex:W3106215946 omid:br/062501777134",
    "title": "OpenCitations, An Infrastructure Organization For Open Scholarship",
    "author": "Peroni, Silvio [orcid:0000-0003-0530-4305 omid:ra/0614010840729]; Shotton, D M [orcid:0000-0001-5506-523X omid:ra/0621010775619]",
    "pub_date": "2020-02",
    "issue": "1",
    "volume": "1",
    "venue": "Quantitative Science Studies [issn:2641-3337 openalex:S4210195326 omid:br/062501778099]",
    "type": "journal article",
    "page": "428-444",
    "publisher": "Mit Press [crossref:281 omid:ra/0610116105]",
    "editor": ""
}
```

| Field | Meaning |
|-------|---------|
| `id` | All known identifiers for this work (DOI, OMID, OpenAlex ID, etc.) |
| `title` | Title of the work |
| `author` | Semicolon-separated list of authors, each with their ORCID and OMID when available |
| `pub_date` | Publication date |
| `venue` | Journal or book where the work was published, with its identifiers |
| `volume` | Volume number |
| `issue` | Issue number |
| `page` | Page range |
| `type` | Type of the work (e.g. `journal article`, `book chapter`) |
| `publisher` | Publisher name and identifiers |
| `editor` | Editors, if applicable |

The Meta API supports more identifier types than the Index API:

| Prefix | Name |
|--------|------|
| `doi` | Digital Object Identifier |
| `pmid` | PubMed Identifier |
| `pmcid` | PubMed Central Identifier |
| `omid` | OpenCitations Meta Identifier |
| `openalex` | OpenAlex Identifier |
| `issn` | International Standard Serial Number (journals) |
| `isbn` | International Standard Book Number (books) |

## Step 4: Authenticate your requests

Everything above works without authentication. If you plan to make frequent requests, we recommend getting a free access token.

The token allows OpenCitations to measure API usage in an anonymous way, without tracking personal data. More information on how it works: [opencitations.net/accesstoken](https://opencitations.net/accesstoken).

To get started:

1. Go to [opencitations.net/accesstoken](https://opencitations.net/accesstoken)
2. Get your token
3. Include it in your requests:

::::{tab-set}
:sync-group: code

:::{tab-item} curl
:sync: curl
```bash
curl -H 'Authorization: YOUR-OPENCITATIONS-ACCESS-TOKEN' \
  'https://api.opencitations.net/index/v2/citation-count/doi:10.1162/qss_a_00023'
```
:::

:::{tab-item} Python
:sync: python
```python
session = requests.Session()
session.headers["Authorization"] = "YOUR-OPENCITATIONS-ACCESS-TOKEN"

# Use session instead of requests for all your calls
result = session.get(
    "https://api.opencitations.net/index/v2/citation-count/doi:10.1162/qss_a_00023"
).json()
```
:::

::::

:::{admonition} Rate limits
:class: warning
API calls are rate-limited to **180 requests per minute** per IP address. This limit exists to guarantee fair access to the service for everyone, regardless of who they are or where they come from. For large-scale data retrieval, use the [database dumps](https://download.opencitations.net/) instead.
:::

## What you've learned

At this point you know how to:

- Count citations for any paper using `/index/v2/citation-count/`
- Retrieve the full list of citations using `/index/v2/citations/`
- Look up bibliographic metadata using `/meta/v1/metadata/`
- Identify works using DOIs, PubMed IDs, and OMIDs
- Read the response fields, including the OCI, timespan, and self-citation flags

## Next steps

- [**Index API v2 full documentation**](https://api.opencitations.net/index/v2): all available operations, parameters, and examples
- [**Meta API v1 full documentation**](https://api.opencitations.net/meta/v1): all metadata operations, including search by author and editor