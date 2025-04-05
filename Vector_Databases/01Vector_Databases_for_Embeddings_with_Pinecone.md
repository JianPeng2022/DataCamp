## 01 Introduction to Pinecone Indexes
**Indexes**
- store vectors
- serve queries and other vector manipulations 
- index contains records for each vector, including metadata
- can create multiple indexes
- 2 types:
  - Pod-based:
    - choose hardware to create the index -> pods
    - pod type determines storage, query latency, query throughout
  - serverless:
    - no resource management
    - indexes scale automatically
    - run on cloud and store in blob
    - easier to use and often lower cost
  
  **creating indexes**
  from pinecone import Pinecone, ServerlessSpec

  pc = Pinecone(api_key = "API_KEY")

  pc.create_index(
    name = 'ai-index', dimension =1314, 
    metric ='dotproduct',spec =ServerlessSpec(cloud='aws', region='us-east-1')
  )
metric = cosine, euclidean, dotproduct
  pc.list_indexes()

**connecting to the index**

index = pc.Index('ai-index')
pc.delete_index(name="ai-index")

index.describe_index_stats()

output: *{'dimension':0.0, 'namespaces': {}, 'total_vector_count':0}*

A index has many namespaces, a namespaces:
- containers for partitioning indexes
- separate datasets
- data versioning
- separate groups

first, we should check the dimensionality:
  - *vector_dims = [len(vector['values'])== 1536 for vector in vectors]*
  - *all(vector_dims)*

Upserting vectors: *index.upsert(vectors=vectors)*

Read units: measure of the resources consumed during read operations: fetching 10 records -> 1 RU;


Accessing vectors
- Fetching: retrieve vectors based on their IDs
  - *index.fetch(ids=['0', '1'] namespace='namespace1')*
- Querying: retrieve similar vectors to an input vector
  - *index.query(vector=[-0.22333,...], top_k=3, include_values=True)*
- Metadate filtering
  - *index.query(vector=[...],filter={"genre":{"$eq":"documentary"}, "year":2019}, top_k=1, include_metadatas=True)*
  - $eq-equal to(bumber, string,boolean), $ne,$gt,$gte,$lt,$lte,$in,$nin

Updating values and metadata
- *index.update(id='1',values=[], set_metadata={"genre":"thriller", "ratings":4}*

Deleting vectors
- *index.delete(ids=[...])*

**Batching upserts**

Upserting limitations: limited rate of requests, size of requests. -> Batching: breaking requests up into smaller *chunks*.

def chunks(iterable, batch_size=100):
  
  it= iter(iterable)
  chunk = tuple(itertools.islice(it, batch_size))
  
  while chunk:
    
      yield chunk
      
      chunk=tuple(itertools.islice(it, batch_size))

Sequential batching: splitting requests and sending them sequentially one-by-one
- *pc.Pinecone(api_key="") index = pc.Index('') for chunk in chunk(vectors): index.upsert(vectors=chunk)*
- solve rate and size limiting, but slow

So parallel batching: splitting requests and sending them in parallel
- pc =Pinecone(api_key="", pool_threads=30)  with pc.Index('', pool_threads=30) as index: async_results = [index.upsert(vectors=chunk,async_req=True) for chunk inchunks(vectors, batch_size=100)] [async_result.get() for async_result in async_results]