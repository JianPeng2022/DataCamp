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
    name = 'ai-index', dimension =1314, spec =ServerlessSpec(cloud='aws', region='us-east-1')
  )

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