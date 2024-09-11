# Ollama Setup for MacBook Pro

This guide provides instructions for setting up Ollama on a MacBook Pro. Follow these steps to get Ollama working with LibreChat.

## Prerequisites
- LibreChat repository cloned locally

## Step 1: Create Docker Compose Override File

1. Locate the `docker-compose.override.yml.example` file in the main directory of the LibreChat repo folder.
2. Duplicate this file and rename it to `docker-compose.override.yml`.

## Step 2: Modify Docker Compose Override File

1. Open your newly created `docker-compose.override.yml` file.
2. Uncomment the following code in the file:

```yaml
services:
  # USE LIBRECHAT CONFIG FILE
  api:
    volumes:
      - type: bind
        source: ./librechat.yaml
        target: /app/librechat.yaml
```

3. Leave everything else commented out. (You can leave the `rag_api` service uncommented if you're using RAG.)
4. Save the file.

## Step 3: Update LibreChat Configuration

1. Open the `librechat.yaml` file.
2. Add the following lines of code under the "custom:" section:

```yaml
custom:
  - name: "Ollama"
    apiKey: "ollama"
    baseURL: "http://host.docker.internal:11434/v1/chat/completions" 
    models:
      default: [
        "llama2",
        "llama3:latest",
        "mistral",
        "codellama",
        "dolphin-mixtral",
        "mistral-openorca"
      ]
    fetch: true
    titleConvo: true
    titleModel: "current_model"
    summarize: false
    summaryModel: "current_model"
    forcePrompt: false
    modelDisplayLabel: "Ollama"
```

3. Save the file.

**Note:** Don't worry about the existing list of models in this code. Since `fetch` is set to `true`, it will automatically pull all the existing models you have for Ollama.

## Step 4: Set Environment Variable

Open a terminal window and set the environment variable for your Ollama endpoint:

```bash
launchctl setenv OLLAMA_HOST "0.0.0.0:11434"
```

## Step 5: Run Ollama Server

In the same terminal window, run the following command to start your Ollama server:

```bash
ollama serve
```

## Step 6: Test Ollama Endpoint

1. Open a new terminal window.
2. Run the following command to test if your Ollama endpoint can handle requests:

```bash
curl http://localhost:11434/api/chat -d '{
  "model": "llama3.1",
  "messages": [
    {
      "role": "user",
      "content": "What is the capital city of Virginia?"
    }
  ],
  "stream": false
}'
```

**Note:** Feel free to select a model of your choice.

If you don't get a proper response, something may be wrong. (Troubleshooting steps will be added soon.)

## Step 7: Deploy with Docker

Go back to your IDE and run the Docker deployment commands:

For a fresh deployment:

```bash
docker compose down
docker system prune -a
docker compose up --build
```

For a regular deployment:

```bash
docker compose up --build
```

## Step 8: Use Ollama in LibreChat

After successful deployment, you should see Ollama as an option for LLM endpoints in the LibreChat UI.

<image src="image.png" alt="alt text" style="width: 80%;">