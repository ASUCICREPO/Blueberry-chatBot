# AI-Powered Blueberry Farming Assistant with Admin Dashboard

A comprehensive chatbot application that provides real-time guidance on blueberry farming practices, powered by AWS Bedrock and featuring an administrative dashboard for content management and analytics.

This application combines natural language processing capabilities with a knowledge base of blueberry farming expertise to deliver accurate, context-aware responses to farmers' queries. The system includes a user-friendly chat interface, multilingual support, and an administrative portal for managing content and monitoring user interactions.

The application features a serverless architecture built on AWS services, with real-time communication through WebSockets, secure file management, and detailed analytics. Key features include:
- AI-powered responses using AWS Bedrock with Claude 3.5 Sonnet
- Automated email notifications for queries requiring expert attention
- Secure document management system for knowledge base updates
- Real-time chat with streaming responses
- Administrative dashboard with analytics and content management
- Multi-language support (English/Spanish)
- Session logging and analysis capabilities

## Disclaimers
Customers are responsible for making their own independent assessment of the information in this document.

This document:

(a) is for informational purposes only,

(b) references AWS product offerings and practices, which are subject to change without notice,

(c) does not create any commitments or assurances from AWS and its affiliates, suppliers or licensors. AWS products or services are provided "as is" without warranties, representations, or conditions of any kind, whether express or implied. The responsibilities and liabilities of AWS to its customers are controlled by AWS agreements, and this document is not part of, nor does it modify, any agreement between AWS and its customers, and

(d) is not to be considered a recommendation or viewpoint of AWS.

Additionally, you are solely responsible for testing, security and optimizing all code and assets on GitHub repo, and all such code and assets should be considered:

(a) as-is and without warranties or representations of any kind,

(b) not suitable for production environments, or on production or other critical data, and

(c) to include shortcuts in order to support rapid prototyping such as, but not limited to, relaxed authentication and authorization and a lack of strict adherence to security best practices.

All work produced is open source. More information can be found in the GitHub repo.

## Repository Structure
```
.
├── buildspec.yml              # AWS CodeBuild configuration for CI/CD
├── cdk_backend/              # AWS CDK infrastructure code
│   ├── bin/                  # CDK app entry point
│   ├── lambda/               # Lambda functions for various services
│   │   ├── adminFile/        # Admin file management handler
│   │   ├── cfEvaluator/      # Chat flow evaluation logic
│   │   ├── email/           # Email notification service
│   │   ├── logclassifier/   # Session log classification
│   │   └── websocketHandler/ # Real-time communication handler
│   └── lib/                 # CDK stack definitions
├── deploy.sh                # Deployment automation script
└── frontend/               # React-based web application
    ├── public/             # Static assets
    └── src/
        ├── Components/     # React components for UI
        └── utilities/      # Shared utilities and contexts
```

# Deployment Instructions
## Common Prerequisites

- Fork this repository to your own GitHub account (required for deployment and CI/CD):
  1. Navigate to https://github.com/ASUCICREPO/osu-blueberry
  2. Click the "Fork" button in the top right corner
  3. Select your GitHub account as the destination
  4. Wait for the forking process to complete
  5. You'll now have your own copy at https://github.com/YOUR-USERNAME/osu-blueberry

- Obtain a GitHub personal access token with repo permissions (needed for CDK deployment):
  1. Go to GitHub Settings > Developer Settings > Personal Access Tokens > Tokens (classic)
  2. Click "Generate new token (classic)"
  3. Give the token a name and select the "repo" and "admin:repo_hook" scope
  4. Click "Generate token" and save the token securely
  For detailed instructions, see:
  - https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens

- Route 53 Identity for SES (Prerequisite):
  1. AWS Console → SES → Identity Management → **Domains**  
  2. Click **Verify a New Domain** and enter your domain (e.g., `example.com`)  
  3. In Route 53, add the provided TXT, MX and CNAME records  
  4. Wait until the domain status shows **verified**

- Enable the following AWS Bedrock models in your AWS account:
  - `TITAN_EMBED_TEXT_V2_1024`
  - `ANTHROPIC_CLAUDE_HAIKU_V1_0`
  - `ANTHROPIC_CLAUDE_3_5_SONNET_V2_0`
  - `NOVA_LITE`
  
  To request access to these models:
  1. Navigate to the AWS Bedrock console
  2. Click "Model access" in the left navigation pane
  3. Click "Manage model access."
  4. Find each model in the list and select the checkbox next to it
  5. Click "Save changes" at the bottom of the page
  6. Wait for model access to be granted (usually within minutes)
  7. Verify access by checking the "Status" column shows "Access granted"

  Note: If you don't see the option to enable a model, ensure your AWS account 
  and region support Bedrock model access. Contact AWS Support if needed.
- AWS Account Permissions 
   - Ensure permissions to create and manage AWS resources like S3, Lambda, Knowledge Bases, AI Agents, Neptune, Amplify, Websocket, etc.  
   - [AWS IAM Policies and Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)


## Deployment Using AWS CodeBuild and AWS Cloudshell
### Prerequisites

- Have access to CodeBuild and AWS Cloudshell

### Deployment

1. Open AWS CloudShell in your AWS Console:
   - Click the CloudShell icon in the AWS Console navigation bar
   - Wait for the CloudShell environment to initialize

2. Clone the repository (Make sure to have your own forked copy of the repo and replace the link with the forked repository link):
```bash
git clone https://github.com/<YOUR-USERNAME>/osu-blueberry
cd osu-blueberry/
```

3. Deploy using the deployment script (recommended):
The script would prompt you for variables needed for deployment.
```bash
chmod +x deploy.sh
./deploy.sh
```

## Manual CDK Deployment
### Prerequisites

1. **AWS CLI**: To interact with AWS services and set up credentials.

   - [Install AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
     
2. **npm**  
   - npm is required to install AWS CDK. Install npm by installing Node.js:  
     - [Download Node.js](https://nodejs.org/) (includes npm).  
   - Verify npm installation:  
     ```bash
     npm --version
     ```
3. **AWS CDK**: For defining cloud infrastructure in code.
   - [Install AWS CDK](https://docs.aws.amazon.com/cdk/v2/guide/getting_started.html)  
     ```bash
     npm install -g aws-cdk
     ```

4. **Docker**: Required to build and run Docker images for the ECS tasks.  
   - [Install Docker](https://docs.docker.com/get-docker/)  
   - Verify installation:  
     ```bash
     docker --version
     ```

### Deployment

1. Clone the repository (Make sure to fork the repository first):
```bash
git clone https://github.com/<YOUR-USERNAME>/osu-blueberry
cd osu-blueberry/
```

2. **Set Up Your Environment**:
Configure AWS CLI with your AWS account credentials:
  ```bash
  aws configure
  ```

3. Install dependencies:
```bash
cd cdk_backend
npm install
```

4. Bootstrap CDK:
```bash
cdk bootstrap --all \
  -c githubToken=YOUR_GITHUB_TOKEN \
  -c githubOwner=YOUR_GITHUB_USERNAME \
  -c adminEmail=YOUR_ADMIN_EMAIL \
  -c route53EmailDomain=YOUR_DOMAIN \
  -c githubRepo=osu-blueberry
```

5. Deploy the stack:
```bash
cdk deploy --all \
  -c githubToken=YOUR_GITHUB_TOKEN \
  -c githubOwner=YOUR_GITHUB_USERNAME \
  -c adminEmail=YOUR_ADMIN_EMAIL \
  -c route53EmailDomain=YOUR_DOMAIN \
  -c githubRepo=osu-blueberry
```

## Usage

Once the infrastructure is deployed using either of the two approaches:

1. Upload any CSV / PDF files to the S3 Bucket

2. Sync the Knowledge Base:
   - Go to AWS Console > Bedrock > Knowledge bases
   - Select the knowledge base created by the stack
   - Click the "Sync data sources" button
   - Wait for sync to complete (status will show "Available")

3. SES Email Verification (Post-Deployment)
   - An email will be sent from AWS to the provided admin email address for verification.
   - If you can't find the email, check the Spam folder and verify by clicking the given link.

5. Add User in Cognito (Post-Deployment)

    - AWS Console → Cognito → **User Pools** → `YOUR_USER_POOL_ID`  
    - Select **Users and groups** → **Create user**  
    - Fill in **Username**, **Temporary password**, and required attributes (e.g., email)  
    - Click **Create user** (the user will reset their password on first login)  

6. Deploy the Frontend:
   - Go to AWS Console > AWS Amplify
   - Select the app created by the stack
   - Access the application URL provided by Amplify 

7. Using the Application:
   - Once frontend deployment is complete, navigate to the Amplify URL
   - The chat interface will load with example queries


### Troubleshooting
1. WebSocket Connection Issues
- Error: "WebSocket connection failed"
  - Check if the AWS API Gateway WebSocket API is deployed correctly
  - Verify the WebSocket URL in the frontend environment variables
  - Ensure your AWS credentials have appropriate permissions

2. Lambda Function Errors
- Error: "Lambda function timed out"
  - Check CloudWatch logs for detailed error messages
  - Increase the Lambda function timeout in the CDK stack
  - Verify memory allocation is sufficient

3. AI Response Issues
- Error: "Knowledge base not responding"
  - Verify the Bedrock knowledge base is properly configured
  - Check if the S3 bucket contains the required data files
  - Ensure the Lambda function has proper IAM permissions

## Data Flow
The application processes user queries through a multi-stage pipeline that ensures accurate and contextual responses.

```ascii
User Query → WebSocket API → Lambda → Bedrock Agent → Knowledge Base
     ↑                                     ↓
     └──────────── Response ←─────── Email Notification
```

Component interactions:
1. User submits query through WebSocket connection
2. Lambda function processes request and invokes Bedrock Agent
3. Agent queries knowledge base and evaluates confidence
4. High confidence responses (>90%) are returned directly
5. Low confidence queries trigger admin notification workflow
6. Session logs are stored in DynamoDB for analytics
7. File uploads are processed and ingested into knowledge base

## Infrastructure

![Infrastructure diagram](./docs/infra.jpg)

### Architecture Diagram Explanation

- **User → Amplify Front-End**  
  - **1.1** User submits location & question (later their email).  
  - **1.9** Amplify returns the Bedrock agent’s answer or asks for the email when escalation is needed.

- **Amplify → Amazon API Gateway**  
  - **1.2** API Gateway receives the request from Amplify and acts as the single entry point for back-end services.

- **API Gateway → Amazon Bedrock Agent**  
  - **1.3** Gateway forwards the query to the Bedrock Agent.  
  - **1.4** Agent inspects the query and decides whether it can answer directly from its **Bedrock Knowledge Base**.

- **Bedrock Agent ↔ Knowledge Base (S3 Data Source)**  
  - A **sync-up workflow** keeps reference docs in an **S3 bucket** synchronized with the Knowledge Base.  
  - **1.6** Agent retrieves the answer and returns it to API Gateway (**1.7**), which then responds to Amplify (**1.8**).

- **Human-in-the-Loop Escalation via Amazon SES**  
  - **3.2** If the Agent cannot answer, API Gateway uses **Amazon SES** to email the question (and the user’s email) to an **Admin**.  
  - **3.3** Admin receives the email.  
  - **4.1 / 4.2** Admin replies to the user *and* the bot.  
  - **5** Admin’s answer is indexed—written to S3 and ingested into the Knowledge Base, improving future responses.

- **Admin Authentication & Document Management**  
  - **6.1 / 7.1** Admin authenticates through **Amazon Cognito** and accesses an Amplify-hosted portal.  
  - **6.3** Within the portal, the Admin uploads or edits docs in the S3 data source feeding the Knowledge Base.

- **Observability & Analytics Pipeline**  
  - **DynamoDB** stores structured logs/metrics from the Bedrock Agent; raw logs are archived in **S3**.  
  - A lightweight **LLM process** mines those S3 logs for insights.  
  - **Dashboard (7.3)** pulls aggregated data from DynamoDB to provide real-time analytics.  
  - **CloudWatch** captures infrastructure-level logs across the entire stack.

- **Data-Flow Summary**  
  1. **Primary path:** *User → Amplify → API Gateway → Bedrock Agent → Knowledge Base/S3 → User*  
  2. **Escalation path:** *API Gateway → SES → Admin → SES → Knowledge Base/S3*  
  3. **Admin management:** *Cognito-authenticated Amplify app → S3 (documents) + DynamoDB/S3 (logs) → Dashboard*

> This architecture combines a serverless web front-end, a Bedrock-powered retrieval agent, human-in-the-loop escalation, and a full observability layer—yielding immediate answers for users while letting admins curate content and monitor system health in one cohesive workflow.


Lambda Functions:
- `adminFile`: Manages document uploads and knowledge base updates
- `cfEvaluator`: Evaluates chat flow and confidence scores
- `email`: Handles admin notifications
- `logclassifier`: Categorizes and analyzes session logs
- `websocketHandler`: Manages real-time communication

AWS Services:
- Bedrock: AI model and knowledge base
- API Gateway: WebSocket and REST APIs
- DynamoDB: Session and analytics data
- S3: Document storage
- SES: Email notifications
- Cognito: User authentication

Environment Variables:
- `REACT_APP_WEBSOCKET_API`: WebSocket API endpoint
- `REACT_APP_ANALYTICS_API`: Analytics API endpoint
- `REACT_APP_COGNITO_USER_POOL_ID`: Cognito user pool ID
- `REACT_APP_COGNITO_CLIENT_ID`: Cognito client ID
