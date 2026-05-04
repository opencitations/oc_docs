# OpenCitations Documentation

## What is OpenCitations

OpenCitations is an open infrastructure that collects, stores, and publishes bibliographic and citation data from scholarly literature. It is managed by the [Research Centre for Open Scholarly Metadata](https://openscholarlymetadata.org/) at the University of Bologna.

The two main datasets are:

- **OpenCitations Index**: over 2.4 billion citation links between scholarly works. A citation link connects a citing work to a cited work, with metadata about the citation itself (date, timespan, self-citation flags).
- **OpenCitations Meta**: bibliographic metadata for over 129 million publications, including titles, authors, venues, publication dates, and identifiers.

The data comes from multiple sources: Crossref, DataCite, NIH-OCC (PubMed), OpenAIRE, and JALC.

All data is released under a [CC0 public domain dedication](https://creativecommons.org/public-domain/cc0/) and can be freely reused for any purpose, including commercial use.

## What OpenCitations is not

OpenCitations is often confused with services like Scopus, Web of Science, or Google Scholar. There are some important differences to keep in mind:

- OpenCitations does **not** provide full-text articles or PDFs
- OpenCitations does **not** provide abstracts
- OpenCitations does **not** compute bibliometric indicators like h-index or impact factor
- OpenCitations does **not** require a subscription or institutional access

OpenCitations provides the raw citation data and bibliographic metadata. It is the building block that tools, platforms, and researchers can use to build their own analyses, indicators, and services on top of.

## How to access the data

There are several ways to access OpenCitations data, depending on your needs.

**[Search](search/index.md)**: a web interface at [search.opencitations.net](https://search.opencitations.net) for looking up citations and references of individual works. No technical knowledge required.

**[REST API](api/quickstart.md)**: programmatic access to citation data (Index API) and bibliographic metadata (Meta API). Useful for building scripts, pipelines, and integrations. The full endpoint documentation is available at [api.opencitations.net](https://api.opencitations.net).

**[SPARQL endpoints](https://sparql.opencitations.net)**: for advanced users who want to query the data using SPARQL.

**[Data dumps](https://download.opencitations.net)**: complete database exports available on Zenodo, Figshare, and the Internet Archive. This is the best option for large-scale analysis when the API rate limits are not enough.

## License

All datasets are released under [CC0](https://creativecommons.org/public-domain/cc0/). The website content is licensed under [CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/). The software is available on [GitHub](https://github.com/opencitations) under the [ISC License](https://opensource.org/licenses/ISC).

## Contacts

For general inquiries: [contact@opencitations.net](mailto:contact@opencitations.net)

For technical support: [tech@opencitations.net](mailto:tech@opencitations.net)