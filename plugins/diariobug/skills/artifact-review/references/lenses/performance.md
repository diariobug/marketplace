# Lens: performance

**Finds:** where the design will be slow, expensive or unable to grow, read from what it states
about volumes, data and algorithms: the performance efficiency characteristic of ISO/IEC
25010:2023 (time behaviour, resource utilization, capacity).

**Applies to:** artifacts with stated volumes, latency or throughput targets, batch work,
growing data, caches, or user-facing paths on a hot route.

## Checklist

1. **Targets:** response time, throughput and capacity are stated with numbers and conditions;
   if not, ask for them before judging anything else.
2. **Volumes:** expected data sizes, request rates and growth over time are stated, and the
   design is checked against them, not against today's data.
3. **Complexity:** operations whose cost grows with data (loops over all records, nested
   iterations, joins without bounds); time and space complexity of the core algorithm.
4. **Round trips:** calls inside loops, chatty interfaces, N+1 queries, sequential calls that
   could run in parallel.
5. **Data access:** queries and their indexes, pagination, full scans, unbounded result sets.
6. **Caching:** what is cached, how it is invalidated, what happens on a cold cache.
7. **Resource limits:** memory held per request, connection pools, file handles, timeouts.
8. **Background work:** batch sizes, scheduling, contention with user traffic.
9. **Cost:** the money a design spends per request or per month where usage is billed.
10. **Measurement:** how the targets will be measured, and where (the test environment that
    can reproduce the load).

## Evidence a finding needs

The quoted design element, the stated or asked-for volume, and the arithmetic or the access
pattern that makes it a problem.

## Cannot see

Actual timings; those need measurement. A performance finding from reading is a risk to
measure, not a measured fact. Say so.

## Common false positives

Optimizing a path with no volume or target behind it; a known small, bounded data set.
