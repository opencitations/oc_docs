---
kernelspec:
  name: python3
  display_name: Python 3
---
# Use cases

Two practical examples of what you can build with the OpenCitations API.

## 1. Batch citation count for a list of DOIs

You have a spreadsheet with thousands of DOIs and you want to know how many times each paper has been cited. This Python script reads a list of DOIs from a text file, queries the API for each one, and writes the results to a new CSV.

```python
import requests
import csv
import time

INPUT_FILE = "list_of_papers.txt"   # one DOI per line
OUTPUT_FILE = "citation_counts.csv"
API_BASE = "https://api.opencitations.net/index/v2/citation-count/doi:"

# Optional: include your access token to help OpenCitations
# monitor anonymous usage of its services
# https://opencitations.net/accesstoken
TOKEN = None

session = requests.Session()
if TOKEN:
    session.headers["Authorization"] = TOKEN

with open(INPUT_FILE) as infile, open(OUTPUT_FILE, "w", newline="") as outfile:
    writer = csv.writer(outfile)
    writer.writerow(["doi", "citation_count"])

    dois = [line.strip() for line in infile if line.strip()]
    total = len(dois)

    for i, doi in enumerate(dois):
        try:
            response = session.get(f"{API_BASE}{doi}", timeout=30)
            if response.status_code == 200:
                data = response.json()
                count = data[0]["count"] if data else "0"
            else:
                count = "error"
        except requests.RequestException:
            count = "error"

        writer.writerow([doi, count])

        if (i + 1) % 100 == 0:
            print(f"Processed {i + 1}/{total}")

        # Respect the rate limit: 180 requests per minute
        time.sleep(0.34)

print(f"Done. Results saved to {OUTPUT_FILE}")
```

The input file `list_of_papers.txt` should look like this:

```
10.1162/qss_a_00023
10.1007/s11192-019-03217-6
10.1038/nature12373
...
```

Running the script and inspecting the output:

```console
$ python3 cit-count.py
Processed 100/5000
Processed 200/5000
...
Done. Results saved to citation_counts.csv

$ cat citation_counts.csv
doi,citation_count
10.1162/qss_a_00023,101
10.1007/s11192-019-03217-6,59
10.1038/nature12373,1514
...
```

:::{admonition} Rate limits and large datasets
:class: warning
At 180 requests per minute, processing 5000 DOIs takes about 28 minutes. If you need citation data for tens of thousands of papers, consider using the [database dumps](https://download.opencitations.net/) instead.
:::

## 2. Live citation counts on your website

If you have a blog or a personal website (like a GitHub Pages site) where you list your publications, you can show a live citation count next to each paper using a few lines of JavaScript.

The count is fetched from the OpenCitations API every time a visitor opens the page, so it stays up to date without any manual work.

Add this snippet wherever you want the count to appear, and replace the DOI with yours:

```html
<span id="cite-count">...</span> citations

<script>
  fetch("https://api.opencitations.net/index/v2/citation-count/doi:10.1162/qss_a_00023")
    .then(function(r) { return r.json(); })
    .then(function(data) {
      document.getElementById("cite-count").textContent = data[0].count;
    });
</script>
```

Here is the same call executed with Python to show the real result:

```{code-cell} python
import requests

response = requests.get(
    "https://api.opencitations.net/index/v2/citation-count/doi:10.1162/qss_a_00023"
)
data = response.json()
print(f'Results: {data[0]["count"]} citations')
```