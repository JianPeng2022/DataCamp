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