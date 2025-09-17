# Company Policy Assistant (Spring AI + AWS Bedrock + Qdrant)

A Spring Boot app that lets you **upload company policy PDFs** and then **ask questions** about them.  
Under the hood it uses:

- **Spring AI** for chat + embeddings
- **AWS Bedrock** to access OpenAI's `gpt-4o-mini` model for conversational answers and Amazon's `titan-embed-text-v2` for embeddings
- **Qdrant** as a vector store for policy chunks

---

## Quick start

### 1) Prereqs
- Docker + Docker Compose
- AWS credentials with access to Amazon Bedrock (export `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and optionally `AWS_SESSION_TOKEN`)
- Bedrock access to the OpenAI `gpt-4o-mini` model (and an embedding model such as `amazon.titan-embed-text-v2`)
### 2) Build and Start
```bash
docker compose build
docker compose up -d
```

The compose file starts:

- qdrant on http://localhost:6333 (HTTP) and grpc://localhost:6334 (gRPC)
- the Spring Boot app on http://localhost:8080 (configured to call AWS Bedrock)

Before running `docker compose up` you can optionally create a `.env` file to override defaults:

```env
AWS_REGION=us-east-1
CHAT_MODEL=openai.gpt-4o-mini
EMBED_MODEL=amazon.titan-embed-text-v2
```

## Endpoints

### 1) Load policies (ETL)

```bash
POST /v1/etl/load
Content-Type: multipart/form-data
Form field: file=<your PDF>
```

Example

```bash
curl -X POST "http://localhost:8080/v1/etl/load" \
-F "file=@/path/to/PolicyHandbook.pdf"
```

### 2) Ask questions (Inference)

```bash
POST /v1/inference/ask
Content-Type: application/json
Body: { "question": "<your question>" }
```

Example

```bash
curl -X POST "http://localhost:8080/v1/inference/ask" \
  -H "Content-Type: application/json" \
  -d '{ "question": "What is the parental leave policy?" }'
```