```python
import boto3
import json

def retrieve_and_generate_with_prompt_router(
    knowledge_base_id: str,
    prompt_router_arn: str,
    query: str,
    region: str = "us-east-1"
):
    """
    Use Amazon Bedrock KnowledgeBases RetrieveAndGenerate API with a Prompt Router.
    
    Args:
        knowledge_base_id: ID of your Knowledge Base (e.g., "KB-XXXXXXXXXX")
        prompt_router_arn: ARN of the configured Prompt Router
        query: User query to send to the knowledge base
        region: AWS region where Bedrock is available
    
    Returns:
        Generated response from Knowledge Base with citations
    """
    # Create Bedrock Agent Runtime client
    bedrock_agent_runtime = boto3.client(
        service_name="bedrock-agent-runtime",
        region_name=region
    )
    
    response = bedrock_agent_runtime.retrieve_and_generate(
        input={
            "text": query
        },
        retrieveAndGenerateConfiguration={
            "type": "KNOWLEDGE_BASE",
            "knowledgeBaseConfiguration": {
                "knowledgeBaseId": knowledge_base_id,
                "modelArn": prompt_router_arn,  # Use Prompt Router ARN instead of model ARN
                # Optional: Configure retrieval results
                "retrievalConfiguration": {
                    "vectorSearchConfiguration": {
                        "numberOfResults": 5  # Retrieve top 5 results
                    }
                },
                # Optional: Custom inference configuration
                "generationConfiguration": {
                    "inferenceConfig": {
                        "textInferenceConfig": {
                            "maxTokens": 2048,
                            "temperature": 0.7,
                            "topP": 0.9
                        }
                    }
                }
            }
        }
    )
    
    # Extract and return the generated response
    output_text = response["output"]["text"]
    
    # Print citations if available
    if "citations" in response:
        print("Citations:")
        for citation in response["citations"]:
            print(f"- {citation['generatedResponsePart']['textResponsePart']['text']}")
    
    return output_text

# Example usage
if __name__ == "__main__":
    KB_ID = "YOUR_KNOWLEDGE_BASE_ID"  # Replace with your KB ID
    PROMPT_ROUTER_ARN = "arn:aws:bedrock:us-east-1:123456789012:prompt-router/YOUR_PROMPT_ROUTER_NAME"  # Replace with your Prompt Router ARN
    
    query = "What are the key features of Amazon Bedrock Knowledge Bases?"
    result = retrieve_and_generate_with_prompt_router(
        knowledge_base_id=KB_ID,
        prompt_router_arn=PROMPT_ROUTER_ARN,
        query=query
    )
    
    print("Response:")
    print(result)
```

## Prerequisites

**Create a Prompt Router first:**
```python
# Example: Create Prompt Router (run once)
bedrock_client = boto3.client("bedrock", region_name="us-east-1")
prompt_router_response = bedrock_client.create_prompt_router(
    clientRequestToken="unique-token-123",
    promptRouterName="my-knowledge-base-router",
    models=[
        {"modelArn": "anthropic.claude-3-5-sonnet-20240620-v1:0"},
        {"modelArn": "anthropic.claude-3-haiku-20240307-v1:0"}
    ],
    routingCriteria={"responseQualityDifference": 0.1},
    fallbackModel={"modelArn": "anthropic.claude-3-haiku-20240307-v1:0"}
)
print(f"Prompt Router ARN: {prompt_router_response['promptRouterArn']}")
```

## Key Points

- Replace `modelArn` with `prompt_router_arn` in `knowledgeBaseConfiguration`
- Prompt Routers work with Anthropic Claude and Meta Llama model families
- Supports dynamic model selection based on response quality and cost optimization
- Knowledge Base handles retrieval; Prompt Router handles generation model selection
- Response includes citations linking back to source documents

