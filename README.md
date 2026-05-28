Problem Statement :- Every company which have large count of employees like TCS,Wipro etc. has the same structural failure: data locked in separate departments, no way to search across them, and no mechanism to stop the wrong person from seeing the wrong thing.

Solution :- I designed a RAG model which have total of 6 layers (RBAC gate,Query Routing,Scope intersection,Hybrid Retrieval,Grounded Generation,Cited Response). 1)RBAC is Role Based Access which provides securities to each department ,system will looks up who you are and which data categories your role permits.Any blocked category is removed before search begins- not after.
2)25+ keyword banks scan your question for signals (e.g. "salary" → HR, "revenue" → Finance, "log error" → IT). Only relevant silos are searched, not all 5.
3)Routed silos are intersected with allowed silos. If you're a finance analyst asking about IT logs, the IT category is dropped silently before retrieval.
4)ChromaDB runs cosine similarity search. A keyword TF scorer runs in parallel. Final score = 0.7 × semantic + 0.3 × keyword. Chunks below 0.25 threshold are dropped.
5)Top-K chunks are injected into a strict prompt. Ollama qwen2.5 is told to cite every claim with [Source N] and refuse to speculate beyond the context.
6)Answer is returned with a full citation table — source file, category, file type, and relevance score for every chunk used. Complete traceability.
7)Temperature = 0.05 LLMs at default temperature hallucinate freely. Near-zero temperature forces the model to stick to the context it was given — essential for factual enterprise use.
