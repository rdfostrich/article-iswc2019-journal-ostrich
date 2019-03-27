## Evaluation
{:#evaluation}

In this section, we evaluate our proposed storage technique and querying algorithms.
We start by introducing OSTRICH, an implementation of our proposed solution.
After that, we describe the setup of our experiments, followed by presenting our results.
Finally, we discuss these results.

### Implementation
{:#implementation}

OSTRICH stands for _Offset-enabled STore for TRIple CHangesets_,
and it is a software implementation of the storage and querying techniques described in this article
It is implemented in C/C++ and available on [GitHub](https://zenodo.org/record/883008){:.mandatory} under an open license.
In the scope of this work, OSTRICH currently supports a single snapshot and delta chain.
OSTRICH uses [HDT](cite:cites hdt) as snapshot technology.
Furthermore, for our indexes we use [Kyoto Cabinet](http://fallabs.com/kyotocabinet/){:.mandatory},
which provides a highly efficient memory-mapped B+Tree implementation with compression support.
For our dictionary, we use and extend HDT's dictionary implementation.
We compress this delta dictionary with [gzip](http://www.gzip.org/), which requires decompression during querying and ingestion.

We provide a developer-friendly C/C++ API for ingesting and querying data based on an OSTRICH store.
Additionally, we provide command-line tools for ingesting data into an OSTRICH store,
or evaluating VM, DM or VQ triple pattern queries for any given limit and offset against a store.
Furthermore, we implemented [Node JavaScript bindings](https://zenodo.org/record/883010){:.mandatory} that
expose the OSTRICH API for ingesting and querying to JavaScript applications such as [Comunica](cite:cites comunica).
We used these bindings to [expose an OSTRICH store](http://versioned.linkeddatafragments.org/bear){:.mandatory}
containing a dataset with 30M triples in 10 versions using [TPF](cite:cites ldf), with the [VTPF feature](cite:cites vtpf).

### Experimental Setup

As mentioned before in [](#related-work), we evaluate our approach using the BEAR benchmark.
For a complete comparison with other approaches, we re-evaluated BEAR's Jena and HDT-based RDF archive implementations.
After that, we evaluated OSTRICH for the same queries and datasets.
We were not able to extend this benchmark with other similar systems such as X-RDF-3X, RDF-TX and Dydra,
because the source code of systems was either not publicly available,
or the system would require additional implementation work to support the required query interfaces.
Our experiments were executed on a 64-bit
Ubuntu 14.04 machine with 128 GB of memory and a
24-core 2.40 GHz CPU.

### Results

In this section, we present the results of our evaluation.
We report the ingestion results, compressibility, query evaluation times for all cases and offset result.
All raw results and the scripts that were used to process them are available on [GitHub](https://github.com/rdfostrich/ostrich-bear-results/){:.mandatory}.

Figures [3](#results-bearb-hourly-vm-sumary), [4](#results-bearb-hourly-dm-summary) and [5](#results-hourly-daily-vq-summary)
show the query duration results for the BEAR-B queries on the complete BEAR-B-hourly dataset for all approaches.
OSTRICH again outperforms Jena-based approaches in all cases.
HDT-IC is faster for VM queries than OSTRICH, but HDT-CB is significantly slower, except for the first 100 versions.
For DM queries, OSTRICH is comparable to HDT-IC, and faster than HDT-CB, except for the first 100 versions.
Finally, OSTRICH outperforms all HDT-based approaches for VQ queries by almost an order of magnitude.

<figure id="results-bearb-hourly-vm-sumary">
<img src="img/query/results_bearb-hourly-vm-summary.svg" alt="[bear-b-hourly vm]" class="plot-graph">
<figcaption markdown="block">
Median BEAR-B-hourly VM query results for all triple patterns for all versions.
</figcaption>
</figure>

<figure id="results-bearb-hourly-dm-summary">
<img src="img/query/results_bearb-hourly-dm-summary.svg" alt="[bear-b-hourly dm]" class="plot-graph">
<figcaption markdown="block">
Median BEAR-B-hourly DM query results for all triple patterns from version 0 to all other versions.
</figcaption>
</figure>

<figure id="results-bearb-hourly-vq-summary">
<img src="img/query/results_bearb-hourly-vq-summary.svg" alt="[bear-b-hourly vq]" class="plot-graph">
<figcaption markdown="block">
Median BEAR-B-hourly VQ query results for all triple patterns.
</figcaption>
</figure>

### Discussion

The results from previous section show that the OSTRICH query evaluation efficiency is faster than all Jena-based approaches,
mostly faster than HDT-CB, and mostly slower than HDT-IC.
VM queries in OSTRICH are always slower than HDT-IC,
because HDT can very efficiently query a single materialized snapshot in this case,
while OSTRICH requires more operations for materializing.
VM queries in OSTRICH are however always faster than HDT-CB, because the latter has to reconstruct complete delta chains,
while OSTRICH only has to reconstruct a single delta relative to the snapshot.
For DM queries, OSTRICH is slower or comparable to HDT-IC, slower than HDT-CB for early versions, but faster for later versions.
This slowing down of HDT-CB for DM queries is again caused by reconstruction of delta chains.
For VQ queries, OSTRICH outperforms all other approaches for datasets with larger amounts of versions.
For BEAR-A, which contains only 10 versions in our case,
the HDT-based approaches are slightly faster because only a small amount of versions need to be iterated.
