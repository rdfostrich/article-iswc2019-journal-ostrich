## Abstract
<!-- Context      -->
In addition to their latest version, Linked Open Datasets on the Web
can also contain useful information in or between previous versions.
<!-- Need         -->
In order to exploit this information,
we can maintain history in RDF archives.
Existing approaches either require much storage space,
or they do not meet sufficiently expressive querying demands.
<!-- Task         -->
In this extended abstract, we discuss an RDF archive indexing technique
that has a low storage overhead, and adds metadata for reducing lookup times.
<!-- Object       -->
We introduce algorithms based on this technique for efficiently evaluating versioned queries.
Using the BEAR RDF archiving benchmark,
we evaluate our implementation, called OSTRICH.
<!-- Findings     -->
Results show that OSTRICH introduces a new trade-off regarding storage space, ingestion time, and querying efficiency.
By processing and storing more metadata during ingestion time,
it significantly lowers the average lookup time for versioning queries.
<!-- Conclusion   -->
Our storage technique reduces query evaluation time
through a preprocessing step during ingestion,
which only in some cases increases storage space when compared to other approaches.
This allows data owners to store and query multiple versions of their dataset efficiently,
<!-- Perspectives -->
lowering the barrier to historical dataset publication and analysis.
