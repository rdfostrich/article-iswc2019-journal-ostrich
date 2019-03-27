## Related Work
{:#related-work}

In this section, we discuss existing solutions and techniques for indexing and compression in RDF storage, without archiving support.
Then, we compare different RDF archiving solutions.
Finally, we discuss suitable benchmarks and different query types for RDF archives.
This section does not contain an exhaustive list of all relevant solutions and techniques,
instead, only those that are most relevant to this work are mentioned.

### General RDF Indexing and Compression

RDF storage systems typically use indexing and compression techniques
for reducing query times and storage space.

[RDF-3X](cite:cites rdf3x) is an RDF storage technique that is based
on a clustered B+Tree with 18 indexes in which triples are sorted lexicographically.
These indexes correspond to different triple component orders.
A dictionary is used to compress common triple components.
When evaluating SPARQL queries, optimal indexes can be selected based on the query's triple patterns.
In our storage approach, we will reuse the concept of multiple indexes
and encoding triple components in a dictionary.

[HDT](cite:cites hdt) is a binary RDF representation that is highly compressed
and provides indexing structures that enable efficient querying.
Furthermore, it also uses a dictionary to reduce storage requirements.
HDT archives are read-only, which leads to high efficiency and compressibility,
but makes them unsuitable for cases where datasets change frequently.
Because of these reasons, we will reuse HDT snapshots as part of our storage solution.

### RDF Archiving
{:#related-work-archiving}

Fernández et al. formally define an [_RDF archive_](cite:cites bear) as follows:
_An RDF archive graph A is a set of version-annotated triples._
Where a _version-annotated triple_ is defined as _an RDF triple with a label representing the version in which this triple holds._
Furthermore,
_an RDF version of an RDF archive is the set of triples that exist at a given version in the archive._

Systems for archiving Linked Open Data are categorized 
into [three storage strategies](cite:cites archiving):

- The **Independent Copies (IC)** approach creates separate instantiations of datasets for
each change or set of changes. Example: [SemVersion](cite:cites semversion)
- The **Change-Based (CB)** approach instead only stores change sets between versions. Example: [R&WBase](cite:cites rwbase)
- The **Timestamp-Based (TB)** approach stores the temporal validity of facts. Example: [X-RDF-3X](cite:cites xrdf3x)

These storage strategies can also be combined into _hybrid approaches_,
such as [TailR](cite:cites tailr) that combines the CB and IC approaches.

### RDF Archiving Benchmarks
{:#related-work-benchmarks}

[BEAR](cite:cites bear) is a benchmark for RDF archive systems.
It offers three different categories of datasets with varying dataset sizes and temporal granularity.
Next to that, triple pattern queries for different versioned query types are provided.
BEAR provides baseline RDF archive implementations based on [HDT](cite:cites hdt) and
[Jena's](cite:cites jena) [TDB store](https://jena.apache.org/documentation/tdb/)
for the IC, CB, and TB approaches, but also hybrid IC/CB and TB/CB approaches.
We use this benchmark for evaluation our approach.

### Query atoms

To cover the retrieval demands in RDF archiving,
[three foundational query types were introduced](cite:cites bear),
which are referred to as _query atoms_:

1. **Version materialization (VM)** retrieves data using a query targeted at a single version.
Example: _Which books were present in the library yesterday?_
2. **Delta materialization (DM)** retrieves a query's result change sets between two versions.
Example: _Which books were returned or taken from the library between yesterday and now?_
3. **Version query (VQ)** annotates a query's results with the versions (of RDF archive A) in which they are valid.
Example: _At what times was book X present in the library?_

Typically, VM queries are efficient in storage solutions that are based on IC,
DM queries are efficient in CB solutions,
and VQ queries perform well for TB solutions.
On the other hand, these query types typically perform sub-optimal for other approaches.
With our solution, we aim to make all query types sufficiently efficient.
