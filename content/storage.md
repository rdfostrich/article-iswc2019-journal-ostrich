## Hybrid Multiversion Storage Approach
{:#storage}

In order to efficiently evaluate all query types,
we introduce a hybrid IC/CB/TB storage solution.
Our approach consists of an _initial dataset snapshot_---stored in [HDT](cite:cites hdt)---followed by a _delta chain_.
The delta chain uses multiple compressed B+Trees for a TB-storage strategy (similar to [X-RDF-3X](cite:cites xrdf3x)),
applies dictionary-encoding to triples, and
stores additional metadata to improve lookup times.

Our storage technique is partially based on a hybrid IC/CB approach similar
to the approach followed by [TailR](cite:cites tailr).
This approach starts by a snapshot, and stores changes as deltas that are related to each other
as can be seen in [](#regular-delta-chain).
In order to avoid increasing reconstruction times,
we construct the delta chain in an [aggregated deltas](cite:cites vmrdf) fashion:
each delta is _independent_ of a preceding delta and relative to the closest preceding snapshot in the chain, as shown in [](#alternative-delta-chain).
Hence, for any version, reconstruction in our approach only requires at most one delta and one snapshot,
as opposed to the whole delta chain as needed for approaches like TailR.

<figure id="regular-delta-chain">
<img src="img/regular-delta-chain.svg" alt="[alternative delta chain]" class="plot-delta">
<figcaption markdown="block">
Delta chain in which deltas are relative to the previous delta or snapshot, as done by [TailR](cite:cites tailr).
</figcaption>
</figure>

<figure id="alternative-delta-chain">
<img src="img/alternative-delta-chain.svg" alt="[alternative delta chain]" class="plot-delta">
<figcaption markdown="block">
Delta chain in which deltas are relative to the snapshot at the start of the chain, as part of our approach.
</figcaption>
</figure>

In order to cope with the newly introduced redundancies in our delta chain structure,
we introduce a delta storage method similar to the TB storage strategy,
which is able to compress redundancies within consecutive deltas.
In contrast to a regular TB approach, which stores plain timestamped triples,
we store timestamped triples in a separate store for additions and deletions.
Each addition and deletion store uses three B+tree indexes with a different triple component order (SPO, POS and OSP)
to improve lookup efficiency when querying.

For deletions specifically, we also store the _relative position_ of each triple
inside the delta to the deletion stores.
When querying, this speeds up the process of patching a snapshot's triple pattern subset for any given offset.
This position information serves two purposes:
1) it allows the querying algorithm to exploit offset capabilities of the snapshot store
to resolve offsets for any triple pattern against any version;
and 2) it allows deletion counts for any triple pattern and version to be determined efficiently.
The use of the relative position during querying will be further explained in [](#querying).
