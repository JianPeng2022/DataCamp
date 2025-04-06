## 01 Introduction to Pinecone Indexes
**1.1 Indexes**
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
  
  **1.2 creating indexes**
  from pinecone import Pinecone, ServerlessSpec

  pc = Pinecone(api_key = "API_KEY")

  pc.create_index(
    name = 'ai-index', dimension =1314, 
    metric ='dotproduct',spec =ServerlessSpec(cloud='aws', region='us-east-1')
  )
metric = cosine, euclidean, dotproduct
  pc.list_indexes()

**1.3 connecting to the index**

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

**1.4 Batching upserts**

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

**1.5 Multitenancy and namespaces**

**multitenancy**: serve multiple tenants in isolation, separate different customers' data, security and privacy, reduce query latency.
- 1. Namespaces: reduces the need for additional indexes, but tenants share resources, complex data.
  - created *implicitly* during upsertion if they don't exist: index.upsert(vectors=vector_set1, namespace="namespaces1")
  - querying vectors from namespaces: query_result = index.query(vector=vector, namespace='namespaces1', top_k=3)
  - deleting vectors from namespaces: index.delete(ids=["1", "2"], namespace='namespaces1')
- 2. Metadata filtering: allows querying across multiple tenants, but shared resources, challenging cost tracking.
- 3. separate indexes: physically separates tenants, allocates individual resources, but requires more effort and cost.

## 02 Semantic search with Pinecone
**Semantic search engines**:
- 1. Embed and ingest documents into a Pinecone index
- 2. Embed a user query
- 3. Query the index with the embedded user query
- from openai import OpenAI 
- from pinecone import Pinecone, ServerlessSpec
- client = OpenAI (api_key= "")
- pc = Pinecone(api_key= "")
- pc.create_index(name="semantic-search-datacamp",dimension=1536 spec = ServerlessSpec(cloud='aws', region='us-east-1'))
- index = pc.Index("semantic-search-datacamp")

**ingesting documents to Pinecone index**
- import pandas as pd
- import numpy as np
- from uuid import uuid4
- df=pd.read_csv("ai.csv")
- batch_limit=100
- for batch in np.array_split(df, len(df)/batch_limit):
  - metadatas = [{"text_id": row['id'],"text":row['text'],"title":row['title']} for_, row in batch.iterrows()]
  - texts = batch['text'].tolist()
  - ids = [str(uuid4()) for _ in range(len(texts))]
  - response = client.embeddings.create(input=texts, model="text-embedding-3-small")
  - embeds = [np.array(x.embedding) for x in response.data]
  - index.upsert(vectors=zip(ids, embeds, metadatas), namespace="squad_dataset")

**Retrieval Augmented Generation (RAG)**
- 1. Embed user query
- 2. Retrieve similar documents
- 3. Added documents to prompt
- def retrieve(query, top_k, namespace, emb_model):
  - query_response = client.embeddings.create(input=query, model=emb_model)
  - query_emb = query_respose.data[0].embedding
  - retrieved_docs = []
  - sources = []
  - docs = index.query(vector=query_emb, top_k=top_k, namespace='youtube_rag_dataset', include_metadata=True)
  - for doc in docs['matches']:
    - retrived_docs.append(doc['metadata']['text'])
    - sources.append((docs['metadata']['title'], doc['metadata']['url']))
  - return retrieved_docs, sources