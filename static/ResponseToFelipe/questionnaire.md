Title: Questionnaire
Slug: questionnaire-2
Date: 2026-03-29

# Eduardo Gusmao

> Recife, Brazil | +5581981052040 | eduardo@gusmaolab.org | linkedin.com/in/eduardogade | github.com/eggduzao

# QUESTIONNAIRE

## SQL

### 1. You have a table of user events with duplicate rows due to a pipeline bug. Walk me through how you would deduplicate it, keeping only the latest event per user, without using a temp table.

**1.A. Short - Spoken Answer**

I would solve this with a CTE and a window function. Since we cannot use a temp table, a window function would be the most efficient native approach.

The idea is to partition the data by "user_id" (I will assume it) and then order each user's events by the event timestamp in descending order, so the most recent event gets row number 1. Then I filter for "display" only the rows where row_number = 1.

Conceptually, I think about it in two steps:
- First, rank the records within each user
- Second, keep only the top-ranked one.

**1.B. Detailed Answer**

As mentioned, CTE+Window (ROW_NUMBER) is the fastest way I can think right now. But now, thinking a bit more, I am disconsidering any platform-specific optimization, such as: indexing or certain alternatives like `QUALIFY`, `DISTINCT ON`, or pre-aggregations. Most are available on PostgreSQL. But take the answer above as I was not quick enough to think about these.

In SQL, it would look like this:

```sql
WITH ranked_events AS (
    SELECT
        *,
        ROW_NUMBER() OVER (  -- Window Function: ROW_NUMBER()
            PARTITION BY user_id
            ORDER BY event_timestamp DESC
        ) AS rn
    FROM user_events
)
SELECT *
FROM ranked_events
WHERE rn = 1;
```

If there is a possibility of ties - for example, two events for the same user with the EXACT same timestamp - then I would add a secondary ordering criterion, such as an ingestion timestamp or a surrogate event_id, to make the result deterministic.

I would also think about business semantics. "Latest per user" may not always mean latest globally - sometimes it needs to be latest per user and event type, or latest non-null valid event, depending on the use case.

And if this is happening because of a pipeline bug, I would not stop at the query. I would also look upstream: why are duplicates being generated, whether the pipeline should be made idempotent (i.e. you can execute it with the same input at any time, producing the same output), and whether we need a uniqueness constraint or validation step to prevent the same issue from recurring.

---

### 2. You are writing a query on a 500 GB BigQuery table. What do you check to make sure it will not perform a full table scan?

**2.A. Short - Spoken Answer**

The first thing I check is whether the table is partitioned, because in BigQuery the main protection against a full scan is partition pruning.

Second, I check clustering. If the table is clustered on fields like user_id, event_type, or region, and my query filters or groups by those columns, BigQuery can reduce the amount of data it needs to read inside the selected partitions.

Third, I check the query itself for anti-patterns, such as unnecessary ``SELECT *``, joining before filtering (~50% of issues in my experience, since it is a VALID heuristic and generally good), casting, etc.

Finally, I would use the query validator dry run, as this gives me a quick cost and performance sanity check.

**2.B. Detailed Answer**

First: Check if Table Is Partitioned
So I look at how the table is partitioned - for example by event_date or ingestion_date - and make sure my WHERE clause filters directly on that partition column. If I don't filter on it, or if I wrap it in a function in a way that prevents pruning, BigQuery may scan far more data than necessary.

Second: Check Clustering
Second, I check clustering. If the table is clustered on fields like user_id, event_type, or region, and my query filters or groups by those columns, BigQuery can reduce the amount of data it needs to read inside the selected partitions. If not clustered, this might actually be a design architecture issue/demand, therefore, if someone else is responsible for that; therefore, I will talk to the architect or solution analyst first. But that's one of the most common patterns I've been seeing lately.

Third, I check the query itself for anti-patterns:
- SELECT * unnecessarily
- joining before filtering
- casting or transforming the partition column in the predicate
- scanning many columns when I only need a few

In BigQuery, column selection matters a lot because it is a columnar engine.

I also use the query validator or dry run to estimate bytes processed before actually running the query. That gives me a quick cost and performance sanity check.

So in practice, my checklist is:
1. Partition filter
2. Cluster-aware predicates
3. Select only needed columns
4. Push filters as early as possible
5. Validate bytes scanned before execution

If the table is not partitioned and this is a recurring workload, then I would treat that as a design issue and recommend restructuring the table, because on a 500 GB table, relying only on query discipline is not enough. So, again, if I am not the direct responsible for this choice, I will talk to the architect or DB admin.

---

## PYTHON

### 1. What is the difference between a list and a dictionary in Python? When would you use each?**

**1.A. Short - Spoken Answer**

Lists are MUTABLE odered collection of elements, that allows repetitive elements, and indexed by position (iterable).
Dictionary are MUTABLE key->value pair collection of elements, where the key must be IMMUTABLE. I can access elements by the key, which do not allow repetitive keys, and they are best for direct key->value acess and key-structured file type representation.

In Data Engineering, lists happen mostly on sequential processing, while dictionaries happen mostly in memory-based data ingestion (e.g. parsing JSONs, YAMLs, etc.)

**1.B. Detailed Answer**

First, allow me to make a clear distinction between mutable vs immutable objects in python.
Mutability: Object CAN change IN PLACE after its creation.
Immutability: Object CANNOT change after its creation (I'd have to create a new object).

Different than close-to-machine languages like C, in Python: One always pass a reference to an object. What matters is whether you mutate the object or rebind the name.

Having said that, a list is a MUTABLE ordered (by indexes) collection of elements. Since it is indexed, every item can be accessed by it's index (position) on the list.

For instance:

```python
users = ["ana", "joão", "maria"]
print(users[1])
```
> "João"

Lists are ideal when the order of the elements matter, e.g. in an iteration.

A dictionary, on the other hand is an ALSO MUTABLE collection of key->value pairs.
The access to is made not by the index, but by the keys. It's like a list, but the index is decided by me, and can be any IMMUTABLE object.

For instance

```python
user = {"id": 1024, "name": "ana", "age": 30}
print(user["age"])
```
> 30

This also means that if I have a list or dictionary of "user", I can directly access any user through, for instance, its "unique ID" "id".

**1.C. When to use each one?**

I will use lists when I want:
- A sequence of items
- Order matters
- Duplicate items are allowed
- I want to iterate them IN BATCH

I will use dictionaries when I want:
- Quick key-based access
- I am representing ENTITIES like a record
- I want to avoid linear-time (𝒪(n)) search
- I need structure, e.g. JSON or YAML

---

### 2. How do you loop through a list of numbers and print only the even ones?

**2.A. Short - Spoken Answer**

Sure, this one is a bit more direct. To show how I would do it clearly and readable even to non-professionals, I would prefer to show the "for" loop version.

First, I must explain that our trick on looking for even and odd numbers, fast, is using "modulo" arithmetic. i.e., a number `X` will always be even if `X%2==0` (rest of the division by 2 is zero) and odd if `X%2==1` (rest of the division by 2 is 1). So, assumint we have a list "numbers":

```python
numbers = [1, 2, 3, 4, 5, 6]
```

Looping and printing require only two flow control mechanisms: A loop (for) and a check (if):

```python
for n in numbers:   # <- Here every 'n' variable will contain a value of 'numbers' per loop.
    if n % 2 == 0:  # <- Here, we check whether the current 'n' is even
        print(n)    # <- If the check is successful, we print the current 'n'
```
> 2
> 4
> 6

**2.B. Detailed Answer**

But, in Python, we have certain "idioms". These are mechanisms we like to use either to improve code readability, cleanliness, or for efficiency purposes.

If I wanted to make the solution a bit more "Pythonic" (as we say), I could use a list comprehension:

```python
even_numbers = [n for n in numbers if n % 2 == 0]
print(even_numbers)
```
> \[2, 4, 6\]

To add more spice, if you want a LAZY EVALUATION (i.e. very useful when streaming data, because it does not carry the list in memory) you could use a generator:

```python
even_numbers = (n for n in numbers if n % 2 == 0)
```

If you want something faster, you can enter the vectorized world of NumPy (for big lists, in small lists the overhead would actually make everything slower):

```python
import numpy as np

vec = np.array(numbers)
even_numbers = vec[vec % 2 == 0]
```

Finally, if you want a "micro/nanossecond-greedy improvement", instead of using modulo arithmetic, you could use bit-wise check (but it is VERY bad for readability and code reviews):

```python
even_numbers = [n for n in numbers if not n & 1]
```

---

## DESIGN

### 1. What is the difference between an idempotent pipeline and an exactly-once pipeline? Can you have one without the other?

This question is more conceptual; I would answer by separating the ideas first.

**1.A. Short - Spoken Answer**

In an idempotent pipeline, you have determinism (the same result) given input and time (when you execute it). Operating the same pipeline over a fixed input for X times, no matter when, will result in the same behavior. In Data Engineer, this is usually used on upserts (i.e. “INSERT ... ON CONFLICT DO UPDATE”), deduplication by ID, writing outputs that can be overwritten, etc.

In an exactly-once pipeline, you are guaranteed that each record is processed one AND ONLY ONE time end-to-end. In Data Engineering, this is usually an “initial safety procedure” for ACID transactions. A very well-known example is Kafka’s producers and consumers. In data streaming, idempotent pipelines could generate many duplicates that, depending on the system, could have catastrophic consequences on memory leakage.

Yes, an idempotent pipeline can aim at being an exactly-once pipeline (e.g. at-least-once delivery with retries and no duplicates). And vice-versa because they are orthogonal concepts: idempotency is about the result, while exactly-once is about the process itself . The main reason for that level of pipeline characterization is within systems reliability and conformance to architecture.

**1.B. Detailed Answer**

An idempotent pipeline is one where running the same operation multiple times produces the same final state as running it once. It tolerates retries, duplicates, and replays without corrupting results. This is usually achieved by designing operations to be safe to repeat: e.g., using deterministic keys, upserts, deduplication by IDs, or writing outputs in a way that overwrites the same target. The focus is on state correctness despite repetition, not on how many times processing actually occurred.

Mental Anchor: Idempotent pipelines are the ones that can run at any given time without sacrificing reliability. i.e., it is a property of the FINAL RESULT.

An exactly-once pipeline guarantees that each record is processed one and only one time - no duplicates, no omissions. Thus, it is the design choice for when conformance is relevant. Achieving this requires coordination across ingestion, processing, and sinks (e.g., transactional reads/writes, offset tracking, two-phase commits, idempotent producers/consumers in systems like Kafka). The goal is delivery and processing semantics, ensuring that even in failures or retries, each event contributes exactly once to the result.

Mental Achor: Exactly-Once pipelines are the ones that run once per certain event, and no more than that for such an event. I.e., it is a property of the PROCESSING.

The key difference is that idempotency is a property of the operations and outputs (repeat-safe), while exactly-once is a property of the processing semantics (no repeats happen). In practice, exactly-once is harder and often system-dependent, whereas idempotency is a design strategy you can apply at the application/data level to make pipelines robust under at-least-once delivery.

You can have an idempotent pipeline without exactly-once processing (e.g., at-least-once delivery with retries, but outputs are deduplicated/upserted to the same final state). Conversely, you can aim for exactly-once processing, but if your operations are not idempotent, bugs or edge cases can still corrupt state. In real systems, teams often rely on idempotent design as the practical safety net, even when striving for exactly-once semantics.

The thing is that both concepts are orthogonal, so they can coexist. But the systems operations would require different mechanics. This is why I am ALWAYS talking to architects (when I am not the architect), scientists (when I am not the scientist) and DB manager.

---
