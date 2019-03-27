## Introduction
{:#introduction}

In the area of data analysis,
there is an ongoing need for maintaining the history of datasets.
Such archives can be used for looking up data at certain points in time,
for requesting evolving changes,
or for checking the temporal validity of these data [](cite:cites archiving).
While the [RDF](cite:cites spec:rdf) data model itself is atemporal,
Linked Datasets typically [change over time](cite:cites datasetdynamics) on
[dataset, schema, and/or instance level](cite:cites diachronql).
Such changes can include additions,
modifications, or deletions of complete datasets, ontologies, and separate facts.
While some evolving datasets, such as [DBpedia](cite:cites dbpedia),
are published as separate dumps per version,
more direct and efficient access to prior versions is desired.

In 2015, however, [a survey on archiving Linked Open Data](cite:cites archiving) illustrated the need for improved versioning capabilities,
as current approaches have scalability issues at Web-scale.
They either perform well for versioned query evaluation—at the cost of large storage space requirements—or
require less storage space—at the cost of slower query evaluation.
Furthermore, no existing solution performs well for all existing versioned query types.
An efficient RDF archive solution should have a scalable *storage model*,
efficient *compression*, and *indexing methods* that enable expressive versioned querying [](cite:cites archiving).

In this article,
we argue that supporting both RDF archiving and SPARQL at once is difficult to scale due to their combined complexity.
Instead, we propose an elementary but efficient versioned _triple pattern_ index that can be used as a basis for query engines.
We focus on the performance-critical features of _stream-based results_, query result _offsets_, and _cardinality estimation_,
which allow more memory-efficient processing and more efficient [query planning](cite:cites ldf,rdf3x).
We offer an open-source implementation of this approach, called OSTRICH,
which we evaluate using an existing RDF archiving benchmark.

This article is structured as follows.
In the following section, we start by introducing the related work in [](#related-work).
Next, we introduce our storage approach in [](#storage),
and our querying algorithms in [](#querying).
After that, we present and discuss the evaluation of our implementation in [](#evaluation).
Finally, we present our conclusions in [](#conclusions).
