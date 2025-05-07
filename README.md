# MDS Compass

An AI-powered Slack chatbot providing support for Master of Data Science students at UBC.

## Project Overview

MDS Compass is a chatbot solution that helps MDS students navigate their academic journey through intelligent assistance. Built on the UBC GenAI Toolkit and integrated with Slack, this tool provides contextual support for common student needs.

This MVP (Phase 1) focuses on:
- Student onboarding assistance
- Technical tooling/installation guidance
- Program policy clarification

## Architecture

MDS Compass uses a Retrieval-Augmented Generation (RAG) approach to provide accurate, context-aware responses:

1. **Knowledge Base**: Curated repository of MDS-specific documentation
2. **Retrieval System**: Semantic search to find relevant information
3. **Generation Layer**: GPT-based response synthesis with retrieved context
4. **Slack Integration**: User-friendly interface within student communication channels
5. **Human Oversight**: Faculty monitoring system for quality control

## Getting Started

### Prerequisites

- Node.js (v16+)
- TypeScript
- Docker & Docker Compose
- Slack API credentials
- OpenAI API key

### Installation

```bash
# Clone the repository
git clone https://github.com/ubc/mds-compass.git
cd mds-compass

# Install dependencies
npm install

# Copy and configure environment variables
cp .env.example .env
# Edit .env with your API keys and configuration

# Build the Docker containers
docker-compose build

# Start the application
docker-compose up -d
```

## Knowledge Base Setup

The RAG implementation requires a properly structured knowledge base:

1. Organize documents in the `data/docs` directory by category:
   - `onboarding/`: Program introduction materials
   - `technical/`: Setup guides and tool documentation
   - `policies/`: Program policies and procedures

2. Run the indexing script to process documents:
   ```bash
   npm run index-knowledge-base
   ```

3. Verify index creation in the `data/indices` directory

## Configuration

Key configuration options in `.env`:

```
# API Keys
OPENAI_API_KEY=sk-your-key-here
SLACK_BOT_TOKEN=xoxb-your-token-here
SLACK_SIGNING_SECRET=your-signing-secret

# RAG Configuration
VECTOR_DB_PATH=./data/indices
EMBEDDING_MODEL=text-embedding-ada-002
COMPLETION_MODEL=gpt-4
SIMILARITY_THRESHOLD=0.75
MAX_SOURCES=3

# Application Settings
NODE_ENV=development
LOG_LEVEL=info
HUMAN_OVERSIGHT_CHANNEL=mds-compass-oversight
```

## Usage

Once deployed, students can interact with MDS Compass in Slack:

- Direct message: `@MDS Compass What software do I need to install?`
- In channels: `@MDS Compass Where can I find the program policies?`

## Development

### Local Testing

```bash
# Run in development mode with hot reloading
npm run dev

# Test the Slack integration locally
npx slack-mock-tester
```

### Adding Content to Knowledge Base

1. Add new Markdown/PDF documents to the appropriate category in `data/docs/`
2. Run `npm run index-knowledge-base` to update indices
3. Test queries related to new content

## Monitoring and Oversight

Faculty oversight is available through:

1. Real-time monitoring in the `#mds-compass-oversight` Slack channel
2. Logging dashboard available at `/admin` (requires admin credentials)
3. Weekly digest reports of common questions and issues

## Privacy and Compliance

- No student identifiable data is sent to external LLM services
- All interactions are logged with appropriate privacy controls
- System complies with UBC data governance policies
- See `PRIVACY.md` for complete privacy documentation

## Roadmap

- **Phase 1 (Current)**: Onboarding & Technical Setup
- **Phase 2 (Future)**: Academic Support
- **Phase 3 (Future)**: Career Development

## Contributing

See `CONTRIBUTING.md` for guidelines on contributing to the project.

## License

This project is licensed under the MIT License - see the `LICENSE` file for details.

## Acknowledgments

- UBC Learning Technology Hub
- UBC GenAI Toolkit Team
- Master of Data Science Program

flowchart TD
    subgraph "User Interface"
        A[Student] -->|Questions via Slack| B[Slack Integration]
    end
    
    subgraph "MDS Compass Core"
        B -->|API Request| C[Request Handler]
        C -->|Query| D[Query Processor]
        
        subgraph "RAG Pipeline"
            D -->|Processed Query| E[Embedding Generator]
            E -->|Vector Embedding| F[Vector Database]
            F -->|Retrieved Documents| G[Context Assembler]
            
            subgraph "Knowledge Base"
                KB1[Onboarding Documents]
                KB2[Technical Setup Guides]
                KB3[Program Policies]
            end
            
            KB1 -.->|Indexed Content| F
            KB2 -.->|Indexed Content| F
            KB3 -.->|Indexed Content| F
            
            G -->|Assembled Context| H[LLM Prompt Constructor]
            H -->|Prompt with Context| I[GPT API]
            I -->|Generated Response| J[Response Filter]
        end
        
        J -->|Filtered Response| K[Response Handler]
    end
    
    subgraph "Human Oversight"
        K -->|Selected Interactions| L[Faculty Oversight Channel]
        L -->|Feedback| C
    end
    
    K -->|Final Response| B
    B -->|Chatbot Answer| A

    classDef primary fill:#d1e7dd,stroke:#198754,stroke-width:2px
    classDef secondary fill:#cff4fc,stroke:#0dcaf0,stroke-width:2px
    classDef tertiary fill:#f8d7da,stroke:#dc3545,stroke-width:2px
    classDef knowledge fill:#e2e3e5,stroke:#212529,stroke-width:1px
    
    class A,B primary
    class C,D,E,F,G,H,I,J,K secondary
    class L tertiary
    class KB1,KB2,KB3 knowledge
