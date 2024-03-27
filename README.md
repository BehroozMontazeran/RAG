# Retrieval Augmented Generation (RAG)

## Introduction
In an era dominated by vast amounts of scientific literature, accessing and comprehending relevant information poses a significant challenge for researchers and professionals. my project addresses this issue by implementing a Retrieval Augmented Generation (RAG) system connected to GPT-3.5, a state-of-the-art language model developed by OpenAI and Meta. This system aims to facilitate the extraction and synthesis of information from articles containing 'Intelligence' hosted on PubMed [1](https://pubmed.ncbi.nlm.nih.gov/), a widely used database of biomedical literature.

The primary objective of my project is to provide an intuitive and efficient means for users to query articles stored from PubMed and receive comprehensive and contextually relevant answers to their questions. As none of the LLMs are working with updated data, they all suffer from providing answers based on most recent information. By leveraging the capabilities of GPT-3.5 and integrating it with a retrieval mechanism, I empower users to pose natural language queries and obtain synthesized responses tailored to their information needs based on most recent data.

In this report, I provide an overview of my project, including its motivation, methodology, and implementation details. I outline the key concepts and techniques employed in my approach, offering insights into how the integration of GPT-3.5 and PubMed enhances the information retrieval and synthesis process. Furthermore, I present the results of my experiments and evaluations, showcasing the effectiveness and performance of my RAG system. Finally, I discuss potential applications, limitations, and future directions for extending and refining my approach to meet evolving user needs and technological advancements.

### Data Acquisition
From the pubmed section intelligence [2](https://pubmed.ncbi.nlm.nih.gov/?term=intelligence+%5BTitle%2Fabstract%5D&filter=simsearch1.fha&filter=years.2013-2023&sort=date&size=200), I gathered abstracts between 2013-2023 that contains term 'Intelligence' from pubmed. To do this I provided different technique that is clarified as follow:

1. API:
   - By this approach I downloaded the xml file, which was later processed and the usefull data extracted, using the preprocessing notbook, and finally the csv file of the related data was created. As the whole dataset was too big to be pushed to github, I used hugging-face to host my dataset. This also changed later by storing the dataset and their embedding on opensearch.

2. Crawler:[Outdated]
   - Using this method I implement a crawler using selenium to extract each abstract, its title and the respective authors from Pubmed one by one.

### Data Preprocessing
   During this phase of the project, I engaged in the utilization of XML files sourced from Pubmed, aiming to extract a comprehensive set of 17 metadata attributes accompanied by their corresponding data points. Subsequently, in the subsequent phase, I proceeded to curate an additional dataset comprising solely the unique identifiers (PMID) alongside amalgamated columns encompassing abstracts, titles, publication dates, author information, medical subject headings pertinent to each abstract, and journal titles. The construction of this refined dataset facilitates enhanced analytical capabilities, thereby enabling the exploration and resolution of a wider array of inquiries, as delineated in my project objectives [Types of Questions and Answers](#Types-of-Questions-and-Answers). This augmentation of data granularity and scope contributes to the enrichment of my research endeavors, fostering deeper insights and robust conclusions within the domain under investigation.

### Data Storage

1. OpenSearch [3](https://opensearch.org/docs/latest/about/), a cloud-based platform:
    adopts a split approach for efficient management of data. Regarding storage, the utilization of diverse embedding models necessitated the segmentation of dataset contexts due to the limitations imposed by maximum length constraints. Consequently, each abstract's context was partitioned into chunks, each identified by a relative ID format, such as PMID-chunk_number. These chunks, along with their associated embeddings, context, and pertinent resources, are stored within the OpenSearch infrastructure. Concerning retrieval, a hybrid search methodology is employed, leveraging the capabilities of OpenSearch to retrieve the k most relevant pieces of data efficiently. This approach enhances the effectiveness of information retrieval within the system, facilitating optimized access to pertinent information. Further exploration of the mechanisms underlying this hybrid search strategy may shed light on its efficacy and potential avenues for refinement and improvement.

2. Pinecone: Cloud based, split approach[Outdated]
   - Storage: Uploading embedding vectors on cloud based VectorDB (Pinecone) and respective abstract's metadata in seperate no-SQL DB (FaunaDB). Use a key to map vectors to metadata between the databases.
   - Retrieval: Retrieve a document by first finding top k matches between embedded query and stored vectors (cosine similarity) in Pinecone, use the ids from the top k matches to query text+metadata, implemented via given API keys into my servers backend/frontend

### Data Retrieval
### 1. Embeddings
   - In this methodology, drawn from the Transformer library, the AutoTokenizer module is employed to perform tokenization on the dataset utilizing the pretrained model 'sentence-transformers/all-distilroberta-v1' [4](https://huggingface.co/sentence-transformers/all-distilroberta-v1/blame/e5e0bbabc6e2c6e494a64b5018d1b40775b173a7/README.md). This process is facilitated by the 'RecursiveCharacterTextSplitter' function from the langchain.text_splitter module. Given the constraint that the maximum input length for this model is 512 tokens, yielding an output dimensionality of 768, a decision was made to partition the data into chunks of size 400, with a 50-token overlap between consecutive chunks. This strategy is implemented to preserve the contextual coherence within each abstract. The adoption of character-based splitting ensures a degree of robustness to misspellings, thereby enhancing the model's retrieval capabilities. Admittedly, chunking the data results in an increased number of units, yet it concurrently enhances the efficiency of data retrieval and search operations. This trade-off between granularity and efficiency underscores the pragmatic considerations governing the data preprocessing stage in natural language processing tasks.
### 2. TF-IDF[Outdated]
   - In this Approach I used the TfidfVectorizer from sklearn and set the analyzing level to characters which provided us the misspelling tolerance. Moreover, I used nltk to add the synonyms to the search of most k relevant abstacts. 
## Experimental and Results

### Types of Questions and Answers
The provided final product can answer following questions type:
1. Confirmation Questions [yes or no]:
   - Is Moog the author of article 'CASK Disorders'?

2. Factoid-type Questions [what, which, when, who, how]:
   - Who is Moog?
   - which articles were published in 2015?
   - What is CASK Disorders?
   - How to treat the CASK Disorders?

3. List-type Questions: 
   - List the name of auhtors of article 'CASK Disorders'.

4. Causal Questions [why or how]: 
   - Why lung cancer is deadly? 

5. Hypothetical Questions:
   - What would happen if CASK Disorders is not treated?

● Complex Questions:
   - What is relation of CASK Disorders in increasing the rate of breast cancer?


## References

1. [National Center for Biotechnology Information. (n.d.). PubMed. Retrieved Oct. 30, 2023](https://pubmed.ncbi.nlm.nih.gov/)
2. [National Center for Biotechnology Information. (n.d.). PubMed. Retrieved Nov. 05, 2023](https://pubmed.ncbi.nlm.nih.gov/?term=intelligence+%5BTitle%2Fabstract%5D&filter=simsearch1.fha&filter=years.2013-2023&sort=date&size=200)
3. [OpenSearch. (n.d.). About OpenSearch. Retrieved Feb. 01, 2024](https://opensearch.org/docs/latest/about/)
4. [Hugging Face. (n.d.). README.md. Sentence Transformers - DistilRoBERTa, a distilled version of RoBERTa. Retrieved Feb. 01, 2024](https://huggingface.co/sentence-transformers/all-distilroberta-v1/blame/e5e0bbabc6e2c6e494a64b5018d1b40775b173a7/README.md)