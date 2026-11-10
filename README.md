# etynet-motif-notebook
 
A Jupyter notebook that builds networks from Wiktionary etymology data and analyzes their structure with directed motif analysis.
 
Words are linked to the words they were inherited or borrowed from, and those word-level links are aggregated into a network of languages. The notebook then compares the language network's local structure (3- and 4-node directed motifs) against degree-preserving random networks, and fits a power law to its degree distribution.
 
> **Status:** exploratory research code written for a graduate project currently in progress. It works end to end, but a few parts are rough and deserve a proper refactoring.
 

## Data
 
The input is the raw Wiktextract dump of Wiktionary, available from [kaikki.org](https://kaikki.org/dictionary/rawdata.html):
 
```
https://kaikki.org/dictionary/raw-wiktextract-data.jsonl.gz
```
 
This file is about 2.4 GB compressed, 24 GB uncompressed, and has just over 10 million lines. The data is derived from Wiktionary, which is available under CC BY-SA, and was extracted with [Wiktextract](https://github.com/tatuylonen/wiktextract). Please credit both if you reuse results.

## Requirements
 
- Python 3.12 or newer
- NumPy 2.0 or newer
- `pandas`, `networkx`, `python-igraph`, `matplotlib`, `tailestim`


## Network construction

Word networks are built with words as nodes and the following relations as directed edges:
 
| Type | Meaning | Source |
|---|---|---|
| `inh_from` | word inherited from an ancestor word in another stage or language | Wiktionary `inh` / `inh+` templates |
| `bor_from` | word borrowed from another language | `bor` / `bor+` templates |
| `affix` | word formed from component words | `af` / `affix` templates |
| `form_of` | inflected form of a base word (collapsed in step 3) | Wiktextract `form_of` field |
 
Entries marked as alternative forms (`alt_of`) are dropped before the network is built. Multi-word entries and empty entries are skipped.

Language networks are then constructed with nodes as languages and directed, weighted edges representing the number of words between the two having the above relations.



## Limitations

- Word senses are currently unable to be disambiguated and are treated as a single word. For example, the English word *light* could be the adjective meaning "having little weight" or the verb meaning "to set on fire"; each of which has a different etymology.
- Etymology templates are parsed heuristically. Entries with unusual or missing templates are skipped or may be linked imperfectly, and some exceptions may be silently ignored.
