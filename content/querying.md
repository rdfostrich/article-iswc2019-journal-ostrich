## Versioned Query Algorithms
{:#querying}

In this section, we introduce algorithms for performing VM, DM and VQ triple pattern queries
based on the storage structure introduced in [](#storage).
Each of these querying algorithms are based on result streams, enabling efficient offsets and limits,
by exploiting the index structure from [](#storage).

### Version Materialization

Version Materialization (VM) is the most straightforward versioned query type,
it allows you to query against a certain dataset version.
Our algorithm takes a triple pattern, a version, and a numerical offset as input,
and returns a triple stream as output.

Starting from the initial snapshot, it does a sort-merge join of the deletions stream for the given version.
At the end of the stream, all additions for the given version are appended.
In order to apply the proper offset to the stream,
we iteratively jump to the correct position in the snapshot and deletions streams
based on the relative position that was stored as metadata in each triple.

### Delta Materialization

The goal of delta materialization (DM) queries is to query the triple differences between two versions.
Furthermore, each triple in the result stream is annotated with either being an addition or deletion between the given version range.
Within the scope of this work, we limit ourselves to delta materialization within a single snapshot and delta chain.
Because of this, we distinguish between two different cases for our DM algorithm
in which we can query triple patterns between a start and end version,
the start version of the query can either correspond to the snapshot version or it can come after that.

For the first query case, where the start version corresponds to the snapshot version,
the algorithm is straightforward.
Since we always store our deltas relative to the snapshot,
filtering the delta of the given end version based on the given triple pattern directly corresponds to the desired result stream.
Furthermore, we filter out local changes, as we are only interested in actual change with respect to the snapshot.

For the second case, the start version does not correspond to the snapshot version.
The algorithm iterates over the triple pattern iteration scope of the addition and deletion trees in a sort-merge join-like operation,
and only emits the triples that have a different addition/deletion flag for the two versions.

### Version Query

For version querying (VQ), the final query atom, we have to retrieve all triples across all versions,
annotated with the versions in which they exist.
In this work, we again focus on version queries for a single snapshot and delta chain.
For multiple snapshots and delta chains, the following algorithms can simply be applied once for each snapshot and delta chain.

Our version querying algorithm is again based on a sort-merge join-like operation.
We start by iterating over the snapshot for the given triple pattern.
Each snapshot triple is queried within the deletion tree.
If such a deletion value can be found, the versions annotation contains all versions except for the versions
for which the given triple was deleted with respect to the given snapshot.
If no such deletion value was found, the triple was never deleted,
so the versions annotation simply contains all versions of the store.
Result stream offsetting can happen efficiently as long as the snapshot allows efficient offsets.
When the snapshot iterator is finished, we iterate over the addition tree in a similar way.
Each addition triple is again queried within the deletions tree
and the versions annotation can equivalently be derived.
