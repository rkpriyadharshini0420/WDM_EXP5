### EX5 Information Retrieval Using Boolean Model in Python
### DATE: 15/04/2025
### AIM: To implement Information Retrieval Using Boolean Model in Python.
### Description:
<div align = "justify">
The Boolean model in Information Retrieval (IR) is a fundamental model used for searching and retrieving information from a collection of documents. It operates on the principles of set theory and logic, where documents are represented as sets of terms or words, and queries are expressed as Boolean expressions using logical operators such as AND, OR, and NOT.
  
### Procedure:
1. ***Initialize the BooleanRetrieval class:*** The BooleanRetrieval class is defined to manage the indexing and searching of documents.
2. ***Constructor and Index Initialization:*** The class constructor initializes an empty index to store the inverted index mapping terms to documents.
3. ***Indexing Documents:***
    <p> a) The index_document method is responsible for indexing documents.
    <p> b) Tokenize the text content of documents, converting them into lowercase terms.
    <p> c) For each term in the document, it adds an entry in the index, associating the term with the document ID. </p>
4. ***Fetch Web Page Text:***
    <p>a) The fetch_webpage_text method uses the requests library to fetch content from a given URL.
    <p>b) Extract text content from the fetched HTML using BeautifulSoup.
    <p>c) The extracted text is returned for further processing.
5. ***Boolean Search:***
    <p>a) The boolean_search method performs Boolean searches on the indexed documents.
    <p>b) Tokenize the input query and iterates through its terms.
    <p>c) For each term in the query, it retrieves documents containing that term and performs Boolean operations (AND, OR, NOT) based on the query's structure.

### Program:
```
import numpy as np
import pandas as pd
import re

class BooleanRetrieval:
    def __init__(self):
        self.index = {}
        self.documents_matrix = None
        self.documents = {}

    def index_document(self, doc_id, text):
        terms = text.lower().split()
        print("Document -", doc_id, terms)
        self.documents[doc_id] = text
        for term in terms:
            if term not in self.index:
                self.index[term] = set()
            self.index[term].add(doc_id)

    def create_documents_matrix(self, documents):
        terms = list(self.index.keys())
        num_docs = len(documents)
        num_terms = len(terms)

        self.documents_matrix = np.zeros((num_docs, num_terms), dtype=int)

        for i, (doc_id, text) in enumerate(documents.items()):
            doc_terms = text.lower().split()
            for term in doc_terms:
                if term in self.index:
                    term_id = terms.index(term)
                    self.documents_matrix[i, term_id] = 1

    def print_documents_matrix_table(self):
        df = pd.DataFrame(self.documents_matrix, columns=self.index.keys())
        print("\nDocuments-Term Matrix:")
        print(df)

    def print_all_terms(self):
        print("All terms in the documents:")
        print(list(self.index.keys()))

    def boolean_search(self, query):
        query = query.lower()
        terms = re.findall(r'\w+|and|or|not', query)

        def get_docs(term):
            return self.index.get(term, set())

        result_stack = []
        operator_stack = []

        i = 0
        while i < len(terms):
            token = terms[i]
            if token == "not":
                i += 1
                term = terms[i]
                result_stack.append(set(self.documents.keys()) - get_docs(term))
            elif token in {"and", "or"}:
                operator_stack.append(token)
            else:
                result_stack.append(get_docs(token))

            if len(result_stack) >= 2 and operator_stack:
                right = result_stack.pop()
                left = result_stack.pop()
                op = operator_stack.pop()
                if op == "and":
                    result_stack.append(left & right)
                elif op == "or":
                    result_stack.append(left | right)

            i += 1

        return result_stack[0] if result_stack else set()
```
# Main Program
if __name__ == "__main__":
    indexer = BooleanRetrieval()
```
    documents = {
        1: "Python is a programming language",
        2: "Information retrieval deals with finding information",
        3: "Boolean models are used in information retrieval"
    }

    for doc_id, text in documents.items():
        indexer.index_document(doc_id, text)

    indexer.create_documents_matrix(documents)
    indexer.print_documents_matrix_table()
    indexer.print_all_terms()

    query = input("\nEnter your boolean query (use AND, OR, NOT): ")
    results = indexer.boolean_search(query)

    if results:
        print(f"\nResults for '{query}': Document IDs {results}")
        for doc_id in results:
            print(f"Doc {doc_id}: {documents[doc_id]}")
    else:
        print("\nNo results found for the query.")
```

### Output:

![Screenshot 2025-04-15 152344](https://github.com/user-attachments/assets/43180eda-0ceb-43e9-8e62-3608eef2c060)

![Screenshot 2025-04-15 152439](https://github.com/user-attachments/assets/a76bd526-1df0-42c3-9233-9e938f1c321b)

![Screenshot 2025-04-15 152718](https://github.com/user-attachments/assets/ecd06ecb-f603-4d8a-b499-f29678caf3c3)

### Result:
The program successfully retrieves relevant documents based on Boolean queries like AND, OR, and NOT using an inverted index.
