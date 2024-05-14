# rag-python-demo-assets
-------------------------------
DESCRIPTION
The "Data Ingestion" node in the diagram refers to the process and mechanisms through which data is imported, processed, and stored in a system or database. In the context of this codebase, the 
Data Ingestion
 process is specifically handled by the ingest.py script. This script is responsible for preparing and ingesting movie data into a Couchbase database, enabling the rest of the application to perform searches and retrieve information efficiently.

The script works in the following way:

Environment Configuration: It starts by loading environment variables using the 
dotenv
 library, which are necessary for connecting to Couchbase and OpenAI services.

Database Connection: The script establishes a connection to a Couchbase cluster by calling the 
connect_to_couchbase
 function, which uses the provided connection string, username, and password to authenticate and connect to the database.

Data Preparation: It reads a CSV file containing movie data (specified by MOVIES_DATASET) into a pandas DataFrame, performs some cleaning and preprocessing (e.g., converting fields to numeric types and filling missing values), and converts the DataFrame into a dictionary format suitable for database ingestion.

Embeddings Generation: For each movie, it generates embeddings for the movie overview text using OpenAI's API by calling the 
generate_embeddings
 function. This embeds the textual data into a high-dimensional space, allowing for semantic searches based on the content of the movie's overview.

Data Ingestion: Finally, it iterates over each preprocessed movie record, inserts the movie data along with its generated embeddings into the Couchbase database. This is done by upserting documents into the specified bucket, scope, and collection within the Couchbase cluster.

This ingestion script forms the foundation for the application's data layer, enabling semantic search capabilities by enriching movie data with machine learning generated embeddings and storing it in a format that's optimized for quick retrieval and search.

---------------------
DESCRIPTION
The "Couchbase Integration" node in the diagram represents the components and processes involved in integrating Couchbase functionalities within the codebase. This integration primarily facilitates two key functionalities: storing and retrieving data (including vector data for semantic search) and executing hybrid (semantic + traditional) search queries against the Couchbase database. Let's break down how this works in the context of the provided codebase:

Couchbase Database Connection and Setup: The integration begins with establishing a connection to a Couchbase database cluster, which is done through the Couchbase SDK. Credentials and connection details are provided via environment variables, as seen in both 
hybrid_search.py
 and 
ingest.py
. These scripts ensure a connection to the Couchbase cluster, allowing further operations such as data ingestion and query execution.

CouchbaseVectorStore Integration: The 
CouchbaseVectorStore
 class from the LangChain community package is used to facilitate interactions with vector data stored in Couchbase. This includes embedding textual data (such as movie synopses) using OpenAI's machine learning models and storing these embeddings alongside the original text in the database. The embeddings enable semantic search capabilities, allowing users to perform searches based on the meaning of the text rather than just keyword matches.

Data Ingestion and Embedding Generation: The 
ingest.py
 script handles the ingestion of documents into the Couchbase database. It reads from a dataset, generates embeddings for textual content (e.g., movie overviews) using the OpenAI API, and then stores these documents in a specified Couchbase bucket, collection, and scope. The generation of embeddings is demonstrated in the 
generate_embeddings
 function, highlighting the process of transforming textual data into vector form for semantic search.

Executing Hybrid Search Queries: In 
hybrid_search.py
, the application performs hybrid search queries against the Couchbase database. This process involves generating embeddings for the search query, executing a vector search to find semantically similar documents, and optionally applying additional filters (e.g., release year, IMDB rating, etc.). The search can be conducted through direct SDK calls or via the CouchbaseVectorStore abstraction, demonstrating flexibility in how hybrid searches can be implemented.

In summary, the "Couchbase Integration" node encapsulates the usage of Couchbase for storing and querying data, including advanced vector search capabilities enabled by OpenAI embeddings. This integration allows the application to offer powerful hybrid search functionalities, combining semantic understanding of content with traditional search filters to enhance user experience.


------------

DESCRIPTION
The "Streamlit UI" node in the diagram represents the user interface components and their interactions within the Streamlit application as defined in the hybrid_search.py script. Streamlit is a Python library that simplifies the process of creating and sharing beautiful, custom web apps for machine learning and data science. In the context of this codebase, it is used to build a web application for a hybrid movie search system.

Here's how each component within the "Streamlit UI" node works based on the hybrid_search.py script:

Set Page Configuration: This initializes the Streamlit page with specific settings such as page title, layout, and icon. This is done using the 
st.set_page_config
 method at the beginning of the script.

Input Form: A text input form is created using 
st.text_input
 where users can type a query for searching movies based on their synopsis or title.

Search Options Sidebar: A sidebar that contains various options for the search, including a checkbox to switch between LangChain and the Couchbase Python SDK for the search operation, and a number input to specify the number of results to display. This is implemented using st.sidebar and components like 
st.checkbox
 and 
st.number_input
.

Filters Sidebar: Within the sidebar, users can enable filters and adjust filter parameters like the movie's release year range, minimum IMDB rating, and whether to search within movie titles. This is also part of the st.sidebar section, with filter inputs being created with 
st.slider
, 
st.number_input
, and 
st.checkbox
.

Display Filters: If filters are enabled and the user chooses to display them, the active filter settings are shown in JSON format using 
st.json
.

Submit Button: A button that triggers the search operation. Its state is captured using 
st.button
 and, when clicked, initiates the search based on the entered query and selected options.

Display Search Results: After submitting a search, the results are displayed in a structured format, showing movie titles, posters, synopsis, and other metadata. This is achieved through a combination of st.header, st.columns, st.image, and st.write functions.

Display Error Messages: If there are any issues with the environment setup (e.g., missing environment variables), error messages are displayed using 
st.error
.

This structure allows for a highly interactive and user-friendly interface for searching movies, leveraging the power of Streamlit to handle user input, initiate searches, and display results dynamically.

------
OpenAI
DESCRIPTION
The "OpenAI Integration" node represents the integration and utilization of OpenAI services within the codebase. This integration is crucial for generating text embeddings, which are essential for the hybrid search functionality of the application. Here's a breakdown of how it works in the context of the codebase:

Check Environment Variables: Before integrating OpenAI, the application verifies that all necessary environment variables are set. This includes checking for the presence of the OPENAI_API_KEY, which is essential for authenticating and using the OpenAI API. This process is handled by the 
check_environment_variable
 function, ensuring that the application has all the required information to connect to OpenAI services.

Initialize OpenAI Client: The application initializes the OpenAI client using the OpenAI API key obtained from the environment variables. This initialization enables the application to communicate with OpenAI services for generating embeddings. The client initialization isn't shown directly in the provided code snippets but is implied through the use of the OpenAI() constructor from the openai Python package, as seen in the 
openai_embedding_client
 variable initialization.

Generate Embeddings: The core functionality of this integration is to generate embeddings for text data, which are vector representations of the input text. These embeddings are generated by calling the generate_embeddings function, which uses the OpenAI client to create embeddings using a specified model. The code for generating embeddings is found in the 
generate_embeddings
 function.

Use LangChain OpenAI Embeddings: In addition to using the native OpenAI client for embeddings, the application also demonstrates the ability to generate embeddings using the LangChain OpenAI Embeddings integration. This is highlighted by the instantiation of the OpenAIEmbeddings class from the langchain_openai package with a specified model. The use of LangChain for embeddings is encapsulated within the 
embedding
 variable initialization.

The integration of OpenAI serves a dual purpose: it enables the generation of semantic embeddings for text data, which are then used for vector-based similarity searches within Couchbase, and it showcases the flexibility of employing both direct API calls to OpenAI and the use of higher-level abstractions provided by LangChain. This integration is crucial for the hybrid search feature, allowing users to query movies based on the semantic content of their descriptions, with the option to apply additional filters such as release year and IMDB rating.

couchbase-examples/hybrid-search-demo

-------------------------

The "LangChain Vector Store Integration" node in the diagram represents the process of integrating the LangChain library's Vector Store capabilities with a Couchbase setup to enable hybrid search functionality within the codebase. This integration allows for performing complex searches that combine traditional database query capabilities with vector-based similarity search, leveraging machine learning models for understanding the semantic meaning of text.

Here's how it works in the context of the provided codebase:

Check Environment Variables for Couchbase and LangChain: This step ensures that all necessary configurations for connecting to Couchbase and using LangChain are available. It involves checking environment variables such as the database connection string, username, password, bucket details, and the OpenAI API key for generating embeddings. This is handled by the 
check_environment_variable
 function.

Connect to Couchbase: Establishes a connection to a Couchbase cluster using the provided credentials and connection string, facilitated by the 
connect_to_couchbase
 function. This connection is crucial for storing and retrieving documents and for executing vector and text searches.

Initialize LangChain Vector Store: This involves creating an instance of the CouchbaseVectorStore class from the LangChain library, which acts as an interface to Couchbase's vector storage and search functionality. The 
get_couchbase_vector_store
 function performs this initialization, configuring the vector store with details about the Couchbase cluster, bucket, scope, and collection, as well as the text and embedding keys.

Generate Embeddings using OpenAI: Before executing a search, embeddings for the search query are generated using OpenAI's API, facilitated by the 
generate_embeddings
 function. These embeddings capture the semantic meaning of the query text and are used to perform vector similarity searches against stored embeddings in Couchbase.

Set Index Name for Couchbase: The name of the search index created in Couchbase, which is configured to support vector searches, is specified. This is necessary for directing search operations to the appropriate index.

Set Text and Embedding Keys: Specifies the keys within the Couchbase documents that contain the text descriptions and their corresponding embeddings. This allows LangChain's Vector Store to correctly map input queries and stored documents for similarity comparison.

Perform Hybrid Search with Filters: Executes the hybrid search functionality, combining vector-based similarity searches with traditional database query filters (such as year and rating filters). This is achieved through the LangChain Vector Store's similarity_search_with_score method, which takes the search query, number of results, and any additional search filters as parameters. The search can return results that are semantically related to the query while also matching specified filter criteria.

In summary, the "LangChain Vector Store Integration" node encapsulates the steps required to leverage LangChain's Vector Store with Couchbase for enhanced search capabilities, combining semantic understanding of content with traditional database search features.

couchbase-examples/hybrid-search-demo


------------------

ouchbase Query Execution


The "Couchbase Query Execution" node in the diagram represents the step where the constructed SQL++ query, which includes vector similarities and additional filters (like year and rating), is executed against a Couchbase database. However, it's important to note that the provided codebase does not directly execute SQL++ queries. Instead, it employs a different approach to perform what is conceptually similar to a hybrid search operation using the Couchbase Vector Search capabilities and additional filters.

In the context of the codebase, specifically in 
hybrid_search.py
, the "Couchbase Query Execution" is abstracted by the function call to search_couchbase, which doesn't use SQL++ syntax directly but leverages the Couchbase Python SDK to perform a hybrid search. This involves generating embeddings for the search text and then using these embeddings, along with additional filter criteria, to find matching documents within the Couchbase database.

The vector search functionality is facilitated through the Couchbase Python SDK's 
VectorSearch.from_vector_query
 method, which constructs a vector search request. The request is then executed against a specified index in the Couchbase cluster. The filtering aspects (like year and rating) are handled through the search_options parameter, which allows for additional query refinement beyond just the vector similarity.

Here's a simplified overview of how it works in the code:

Generate Embeddings: The search text is transformed into embeddings using the OpenAI API, as shown in the 
generate_embeddings
 function.

Vector Search Request: A vector search request is created with the generated embeddings and the specified number of results (k) to retrieve, as part of the search_couchbase function.

Execute Search: The vector search request, along with any additional search options (filters), is executed against the Couchbase cluster. This is done through the db_scope.search method, which uses the index specified by index_name.

Fetch Results: The search results are fetched, and each document is accompanied by a relevance score based on the vector similarity and any applied filters.

So, while the diagram conceptually mentions "Couchbase Query Execution", in practice, this corresponds to the execution of a vector search combined with filter criteria using the Couchbase SDK, rather than directly running SQL++ queries.

