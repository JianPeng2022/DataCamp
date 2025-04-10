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