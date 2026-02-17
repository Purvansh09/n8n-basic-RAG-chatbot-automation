# n8n-basic-RAG-chatbot-automation
# RAG Chatbot with n8n Automation

A powerful RAG (Retrieval-Augmented Generation) chatbot built with n8n that automatically ingests documents from Google Drive, stores them in a Pinecone vector database, and provides intelligent responses using OpenAI's GPT models.

## 🌟 Features

- **Automatic Document Ingestion**: Monitors a Google Drive folder and automatically processes new files
- **Vector Database Storage**: Uses Pinecone for efficient semantic search and retrieval
- **AI-Powered Responses**: Leverages OpenAI's GPT models for intelligent, context-aware answers
- **Real-time Chat Interface**: Webhook-based chat trigger for instant user interactions
- **Semantic Search**: Advanced embedding-based retrieval for accurate information extraction

## 🏗️ Architecture

The workflow consists of two main components:

### 1. Document Ingestion Pipeline
- Monitors Google Drive folder for new files
- Downloads and processes documents
- Splits text into manageable chunks
- Generates embeddings using OpenAI
- Stores vectors in Pinecone namespace

### 2. Chat Interface
- Receives user queries via webhook
- Uses AI Agent to orchestrate responses
- Retrieves relevant context from vector database
- Generates responses using GPT model

## 📋 Prerequisites

Before you begin, ensure you have the following:

- [n8n](https://n8n.io/) instance (self-hosted or cloud)
- [Google Drive API](https://developers.google.com/drive) credentials
- [OpenAI API](https://platform.openai.com/) key
- [Pinecone](https://www.pinecone.io/) account and API key

## 🚀 Setup

### 1. Install n8n

```bash
# Using npx
npx n8n

# Using Docker
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

### 2. Configure Credentials

In your n8n instance, set up the following credentials:

#### Google Drive OAuth2
1. Go to **Credentials** → **New**
2. Select **Google Drive OAuth2 API**
3. Follow the authentication flow

#### OpenAI API
1. Go to **Credentials** → **New**
2. Select **OpenAI API**
3. Enter your OpenAI API key

#### Pinecone API
1. Go to **Credentials** → **New**
2. Select **Pinecone API**
3. Enter your Pinecone API key

### 3. Create Pinecone Index

1. Log into your Pinecone console
2. Create a new index named `n8nfirst`
3. Set dimensions to `1536` (for OpenAI embeddings)
4. Choose your preferred metric (cosine recommended)

### 4. Import Workflow

1. Copy the contents of `RAG_chatbot.json`
2. In n8n, go to **Workflows** → **Import from File**
3. Paste the JSON content
4. Save the workflow

### 5. Configure Google Drive Folder

1. Open the **Google Drive Trigger** node
2. Select the folder you want to monitor for new documents
3. The current folder ID is: `1zcpz52DkvANHJGVcn263BAoThC315aRa`
4. Update this to your own folder if needed

### 6. Activate the Workflow

Click the **Active** toggle in the top-right corner to enable the workflow.

## 🔧 Configuration

### Document Processing Settings

**Text Splitter Configuration**:
- Node: `Recursive Character Text Splitter`
- Default chunk size: 1000 characters
- Default overlap: 200 characters

**Vector Store Configuration**:
- Index: `n8nfirst`
- Namespace: `FAQ`
- Embedding model: OpenAI (text-embedding-ada-002)

### Chat Model Settings

**OpenAI Model**:
- Model: `gpt-5-mini` (update to your preferred model)
- Temperature: Default
- Max tokens: Default

## 💬 Using the Chatbot

### Get the Webhook URL

1. Open the **When chat message received** node
2. Copy the webhook URL
3. The chat interface is available at this URL

### Testing

Send a POST request to the webhook URL:

```bash
curl -X POST https://your-n8n-instance.com/webhook/9b1cc857-45a9-4cce-8f98-1e2245f93c63 \
  -H "Content-Type: application/json" \
  -d '{
    "chatInput": "What information do you have?"
  }'
```

Or use the built-in n8n chat interface by visiting the webhook URL in your browser.

## 📊 Workflow Nodes

| Node | Purpose |
|------|---------|
| **Google Drive Trigger** | Monitors folder for new files |
| **Download File** | Downloads file content from Google Drive |
| **Pinecone Vector Store** (Insert) | Stores document embeddings |
| **Embeddings OpenAI** | Generates embeddings for documents |
| **Default Data Loader** | Loads binary document data |
| **Recursive Character Text Splitter** | Splits documents into chunks |
| **When Chat Message Received** | Webhook trigger for chat |
| **AI Agent** | Orchestrates the chat response |
| **OpenAI Chat Model** | Language model for responses |
| **Knowledgebase** (Pinecone) | Retrieves relevant context |
| **Embeddings OpenAI** (Query) | Generates embeddings for queries |

## 🔄 How It Works

### Document Ingestion Flow

```
New File in Drive → Download → Split Text → Generate Embeddings → Store in Pinecone
```

1. **Trigger**: New file detected in Google Drive folder
2. **Download**: File content is downloaded
3. **Processing**: Document is split into chunks
4. **Embedding**: Each chunk is converted to vector embeddings
5. **Storage**: Vectors are stored in Pinecone with namespace "FAQ"

### Chat Response Flow

```
User Query → AI Agent → Retrieve Context → Generate Response → Return Answer
```

1. **Input**: User sends a message via webhook
2. **Agent**: AI Agent receives the query
3. **Retrieval**: Relevant context is fetched from Pinecone
4. **Generation**: OpenAI model generates response with context
5. **Output**: Answer is returned to user

## 🛠️ Customization

### Changing the AI Model

Edit the **OpenAI Chat Model** node:
- `gpt-4` for better quality
- `gpt-3.5-turbo` for faster responses
- `gpt-4o` for vision capabilities

### Adjusting Retrieval Settings

Edit the **knowledgebase** node:
- Modify `topK` to retrieve more/fewer documents
- Adjust similarity threshold for filtering

### Custom Namespaces

To organize different knowledge bases:
1. Change the namespace in both Pinecone Vector Store nodes
2. Use different namespaces for different document types

## 🐛 Troubleshooting

### Common Issues

**"Failed to connect to Pinecone"**
- Verify your API key is correct
- Ensure the index name matches exactly
- Check that dimensions are set to 1536

**"No relevant documents found"**
- Verify documents have been ingested
- Check the namespace matches in both pipelines
- Try adjusting similarity thresholds

**"Google Drive trigger not firing"**
- Ensure workflow is active
- Verify folder permissions
- Check credentials are valid

## 📝 Best Practices

1. **Document Format**: Use PDF, TXT, or DOCX for best results
2. **File Size**: Keep documents under 10MB for optimal processing
3. **Naming**: Use descriptive file names for better context
4. **Organization**: Group related documents in the same folder
5. **Testing**: Test with a few documents before bulk uploads

## 🔐 Security Considerations

- Store API keys in n8n credentials, never in the workflow
- Use environment variables for sensitive data
- Limit Google Drive folder access to necessary users
- Implement rate limiting on the webhook endpoint
- Consider adding authentication to the chat interface

## 📈 Performance Tips

- **Batch Processing**: Upload multiple files at once to minimize processing time
- **Caching**: Enable caching in production environments
- **Indexing**: Regularly optimize your Pinecone index
- **Monitoring**: Set up n8n error workflows for failure notifications

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🙏 Acknowledgments

- [n8n](https://n8n.io/) - Workflow automation platform
- [OpenAI](https://openai.com/) - Language models and embeddings
- [Pinecone](https://www.pinecone.io/) - Vector database
- [Google Drive](https://www.google.com/drive/) - Document storage

## 📞 Support

If you encounter any issues or have questions:
- Open an issue in this repository
- Check the [n8n community forum](https://community.n8n.io/)
- Review [Pinecone documentation](https://docs.pinecone.io/)
- Consult [OpenAI API documentation](https://platform.openai.com/docs)

---

**Made with ❤️ using n8n**
