## 01 Building Augmented Generation (RAG) with LangChain
**Preparing data for retrieval**: 

1. Loading the documents to build up the knowledge base
2. Split them into chunks
3. Embedding them
4. Store embeddings (vectors) in a vector database for future retrieval

LangChain is document loaders: 
- *from langchain_community.document_loaders.csv_loader import CSVLoader*
- *from langchain_community.document_loaders import PyPDFLoader*
- *from langchain_community.document_loaders import UnstructuredHTMLLoader*
- *pdf_loader = PyPDFLoader('rag_paper.pdf')*
- *documents = pdf_loader.load()*
- *print(documents[0].page_content)*
- *print(documents[0].metadata)*

**splitting or chunking**: usually big chunks are slow, difficult to interpret, but small chunks are lack of context.
- *chunk_size*
- *chunk_overlap*: include information beyond the boundary

**Text Splitting, embeddings, and Vector storage**
- *from langchain_text_splitters import CharacterTextSplitter*
- *text_splitter = CharacterTextSplitter(separator=" ", chunk_size = 100, chunk_overlap=10)*
- *chunks = text_splitter.split_text(text)*
- *from langchain_text_splitters import RecursiveCharacterTextSplitter*
- *splitter=RecursiveCharacterTextSplitter(separators=[],chunk_size=100,chunk_overlap=10)*

**Splitting documents**
- *from langchain_community.document_loaders import PyPDFLoader*
- *loader =PyPDFLoader("research_paper.pdf")*
- *documents = loader.load()*
- *splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)*
- *chunks=splitter.split_documents(documents)*

## 02 Building an LangChain Expression Language (LCEL) retrieval Chain

- Input -> RunnablePassthrough -> Question
- Input -> Retriever -> Context
- Question + Context -> Prompt -> LLM -> Parser

**01 Instantiating a retriever**
- *vector_store = Chroma.from_documents(documents=chunks, embedding = embedding_model)*
- *retriever = vector_store.as_retriever(search_type="similarity",search_kwargs={"k":2})*

**02 Creating a prompt template**
- *from langchain_core.prompts import ChatPromptTemplate*
- *prompt = ChatPromptTemplate.from_template("""Use the following pieces of context to answer the questin at the end. If you don't know the answer, say that you don't know. Context: {context} Question:{question})"""*
- *llm=ChatOpenAI(model="gpt-4o-mini",api_key="...",temperature=0*

**03 Building an LCEL retrieval chain**
- *from langchain_core.runnables import RunnablePassthrough*
- *from langchain_core.output_parsers import StrOutputParser*
- *chain = ({"context":retriever, "question":RunnablePassthrough()}) 丨 prompt 丨 llm 丨 StrOutputParser())*
- *result=chain.invoke({"question": "what are the key findings or results presented in the paper"})*

## Improving the RAG Architecture
**Loading and splitting code files**
**Loding Markdown files (.md)**
- *from langchain_community.document_loaders import UnstructuredMarkdownLoader*
- loader = UnstructuredMarkdownLoader("README.md")
- markdown_content = loader.load()
- print(markdown_content[0])

**Loading python files (.py)**
- Integrated into RAG applications for writing or fixing code, creating docs, etc..
- *from abc import ABC, abstractmethod*
- class LLM(ABC):
  - @abstractmethod
  - def complete_sentence(self, prompt):
    - pass
- *from langchain_community.document_loaders import PythonLoader*
- loader = PythonLoader('chatbot.py')
- python_data = loader.load()
- print (python_data[0])

Parsing Python files can be tricky, because it has its own syntax, so we have to **splitting code files**
- python_splitter = RecursiveCharacterTextSplitter(chunk_size=150, chunk_overlap=10)
- chunks = python_splitter.split_documents(python_data)
- for i, chunk in enumerate(chunks[:3]):
  - print(f"Chunk {i+1}: {chunk.page_content}")
  - This result is not good
  
**Splitting by language**
- *from langchain_text_splitters import RecursiveCharacterTextSplitter, Language*
- python_splitter = RecursiveCharacterTextSplitter.from_language(language=Language.PYTHON, chunk_size=150, chunk_overlap=10)
- chunks = python_splitter.split_documents(data)

**Advanced splitting methods**: Context of surrounding text -> splitting on tokens *TokenTextSplitter*
- import tiktoken
- from langchain_text_splitters import TokenTextSplitter
- example_string = "Mary had a little lamb, it's fleece was white as snow."
- encoding = tiktoken.encoding_for_model('gpt-4o-mini')
- splitter = TokenTextSplitter(encoding_name = encoding.name, chunk_size=10, chunk_overlap=2)
- chunks = splitter.split_text(example_string)
- for i, chunk in enumerate(chunks):
  - print(chunk)

**semantic splitting**
- from langchain_openai import OpenAIEmbeddings
- from langchain_experimental.text_splitter import SemanticChunker
- embeddings = OpenAIEmbeddings(api_key"",model='text-embedding-3-small')
- semantic_splitter = SemanticChunker(embeddings=embeddings,breakpoint_threshold_type='gradient', breakpoint_threshold_amount=0.8)
- chunks=semantic_splitter.split_documents(data)


**Optimizing document retrieval**
**Dense**: Encode chunks as a single vector with *non-zero* components. Capturing semantic meaning, but computationally expensive.

**Sparse**: Encode using *word matching* with mostly zero components. Precise, explainable, rare-word handling, but generalizability.

**Sparse retrieval methods**
- TF-IDF: Encodes documents using the words that make the document unique.
- BM25: Helps mitigate high-frequency words from saturating the encoding.
  - from langchain_community.retrievers import BM25Retriever
  - chunks = ["Python was created by Guido van Rossum and released in 1991.","Python is a popular language for machine learning (ML).","The PyTorch library is a popular Python library for AI and ML."]
  - bm25_retriever = BM25Retriever.from_texts(chunks, k=3)
  - results = bm25_retriever.invoke("when was Python created?")
  - print("Most Relevant Document:")
  - print(results[0].page_content)
  - *Most Relevant Document:Python was created by Guido van Rossum and released in 1991.*

BM25 in RAG
 - retriever = BM25Retriever.from_documents(    documents=chunks,     k=5)
 - chain = ({"context": retriever, "question": RunnablePassthrough()}         | prompt         | llm         | StrOutputParser())
 - print(chain.invoke("How can LLM hallucination impact a RAG application?"))

**RAG Evaluation**