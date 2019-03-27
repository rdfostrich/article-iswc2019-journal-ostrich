## Conclusions
{:#conclusions}

In this article, we introduced an RDF archive storage method with accompanied algorithms
for evaluating versioned queries, with efficient result offsets.
By storing additional data during ingestion, we achieve a significant query efficiency improvement.

With OSTRICH, we provide a technique for publishing and querying RDF archives at Web-scale.
And with lookup times of 1ms or less in most cases, OSTRICH is an ideal candidate for Web querying,
as the network latency will typically be higher than that.
At the cost of increased ingestion times, lookups are fast.
Several opportunities exist for advancing this technique in future work,
such as improving the ingestion efficiency, increasing the DM offset efficiency,
and supporting dynamic snapshot creation.
Furthermore, branching and merging of different version can be investigated.

Our approach succeeds in reducing the cost of publishing RDF archives on the Web.
It lowers the barrier towards intelligent clients that require *evolving* data,
with the goal of time-sensitive querying over the ever-evolving Web of data.

<div class="printonly">
<br />
<br />
<br />
</div>
