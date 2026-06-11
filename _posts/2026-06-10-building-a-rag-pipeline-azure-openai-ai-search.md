---
layout: post
title: "Building a RAG Pipeline with Azure OpenAI and AI Search"
date: 2026-06-10 09:00:00 +1000
category: Azure OpenAI
icon: "🔍"
description: "Learn how to build a production-ready RAG pipeline using Azure OpenAI and Azure AI Search — covering chunking, hybrid search, and grounded generation."
---

Here's a scenario I hear constantly from clients: "We've got thousands of internal documents, SharePoint libraries, and PDFs — can we just *ask* our data questions?" The answer is yes, and the pattern that makes it possible is called **Retrieval-Augmented Generation (RAG)**. It's one of the most practical AI architectures you can build on Azure today, and after helping several organisations stand one up, I want to walk you through exactly how it works and how to get started.

## What Is RAG, and Why Does It Matter?

Large language models like GPT-4o are extraordinarily capable, but they have a hard cutoff: they only know what was in their training data. Feed them a question about your company's internal policies or last quarter's sales figures, and you'll get either a hallucination or a polite "I don't know."

RAG solves this by splitting the problem into two steps:

1. **Retrieve** — search your own data store for chunks of content relevant to the user's question
2. **Generate** — pass those chunks as context to the LLM, which then synthesises a grounded answer

The result is a model that *feels* like it knows your business, because at query time, it actually does. No fine-tuning required.

On Azure, the canonical RAG stack is **Azure OpenAI Service** (the LLM) plus **Azure AI Search** (the retrieval engine). Let me walk you through building this end to end.

## The Architecture at a Glance

```
User Question
     │
     ▼
Azure AI Search ──► Relevant document chunks
     │
     ▼
Azure OpenAI (GPT-4o) ──► Grounded answer returned to user
```

Behind the scenes, your documents have already been **chunked**, **embedded** (converted to vector representations), and **indexed** in AI Search. When a query arrives, it gets embedded the same way, and a vector similarity search finds the most relevant chunks — usually the top 3–5. Those chunks become the "context" in the prompt sent to GPT-4o.

## Step 1 — Provision Your Azure Resources

You'll need:

- An **Azure OpenAI** resource with a GPT-4o deployment (or GPT-4-turbo if you prefer)
- A second deployment for embeddings — I use `text-embedding-3-large`
- An **Azure AI Search** resource (Basic tier is fine for POCs; Standard for production)
- An **Azure Storage Account** with a blob container holding your documents

All of this can be spun up via the Azure Portal or with a Bicep template. I keep a reusable template in my repo — using infrastructure-as-code from day one saves a lot of pain later.

## Step 2 — Index Your Documents

This is where most people get tripped up. Raw PDFs and Word docs aren't directly usable — you need to extract text, chunk it, embed it, and push it into AI Search.

Azure AI Search's **integrated vectorisation** feature (now generally available) handles much of this automatically. From the portal:

1. Go to your AI Search resource → **Import and vectorise data**
2. Point it at your blob container
3. Select your embedding model deployment
4. Configure chunking — I typically use **512 tokens** with a **10% overlap** to preserve context across chunk boundaries
5. Run the indexer

Within minutes you'll have a search index with both keyword fields and vector fields, ready for **hybrid search** (keyword + semantic + vector all at once). This hybrid approach consistently outperforms pure vector search in my experience — don't skip it.

## Step 3 — Query Time: Retrieve, Then Generate

Here's a simplified Python snippet showing the core RAG loop:

```python
from azure.search.documents import SearchClient
from azure.search.documents.models import VectorizedQuery
from openai import AzureOpenAI

# 1. Embed the user's question
oai_client = AzureOpenAI(...)
embedding = oai_client.embeddings.create(
    input=user_question,
    model="text-embedding-3-large"
).data[0].embedding

# 2. Retrieve relevant chunks from AI Search
search_client = SearchClient(...)
vector_query = VectorizedQuery(vector=embedding, k_nearest_neighbors=5, fields="content_vector")
results = search_client.search(
    search_text=user_question,       # keyword component
    vector_queries=[vector_query],   # vector component
    query_type="semantic",
    semantic_configuration_name="my-semantic-config",
    top=5
)
context = "\n\n".join([r["content"] for r in results])

# 3. Generate a grounded answer
messages = [
    {"role": "system", "content": "Answer only using the provided context. If the answer isn't in the context, say so."},
    {"role": "user", "content": f"Context:\n{context}\n\nQuestion: {user_question}"}
]
response = oai_client.chat.completions.create(model="gpt-4o", messages=messages)
print(response.choices[0].message.content)
```

That's the core loop. Everything else — streaming, citations, conversation history, UI — is built on top of this foundation.

## A Real-World Example

One organisation I worked with had 8 years of internal HR policies spread across 200+ Word documents. Their HR team was drowning in repetitive questions from employees. We stood up a RAG pipeline in two weeks: AI Search indexed all the docs, a simple Teams bot exposed the chat interface, and suddenly employees could ask "What's the parental leave policy for contractors?" and get an accurate, cited answer in seconds.

The key thing that made it trustworthy? We instructed the system prompt to **always cite the document name and page** it drew from. When users can see the source, they trust the answer — and can verify it themselves.

## Practical Tips I've Learned the Hard Way

**Chunk size matters more than you think.** Too small and you lose context; too large and you waste token budget on irrelevant content. 512 tokens with overlap is a good starting point, but test with your actual data.

**Don't forget semantic ranker.** Azure AI Search's semantic ranker (included in Standard tier) re-scores results using a cross-encoder model. It adds latency (~200ms) but meaningfully improves retrieval quality on longer, nuanced queries.

**System prompt discipline is everything.** Explicitly tell the model to stay grounded in the context and say "I don't know" when the answer isn't there. Without this guardrail, even a well-retrieved RAG pipeline will hallucinate at the edges.

**Monitor with Azure AI Foundry.** Use prompt flow evaluations to track groundedness, relevance, and coherence scores over time. Set up alerts before users start complaining, not after.

---

## Key Takeaways

- **RAG = Retrieve + Generate**: combine Azure AI Search (retrieval) with Azure OpenAI (generation) for answers grounded in your own data
- **Hybrid search wins**: use keyword + vector + semantic ranking together for best retrieval quality
- **Chunk thoughtfully**: 512 tokens with ~10% overlap is a reliable default; tune from there
- **Cite your sources**: always surface the source document in the answer to build user trust
- **Evaluate continuously**: use Azure AI Foundry's built-in evals to track groundedness and catch regressions early

---

Building a RAG pipeline is one of the most impactful things you can do with Azure AI right now — and it's more approachable than it looks once you know the moving parts. If you're just getting started, spin up a free trial of Azure AI Search, point it at a handful of docs, and experience the "aha" moment for yourself.

Have questions or want to discuss this further? Connect with me on LinkedIn or leave a comment below.

---

---
---