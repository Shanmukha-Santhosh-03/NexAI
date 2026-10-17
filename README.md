
# NexAI

NexAI is a multi-service AI application with a React frontend and a Node.js backend. Its backend separates gateway, authentication, chat, agent, and billing responsibilities into individual services.

## Project Architecture

```text
NexAI/
|-- .github/
|   `-- workflows/
|-- backend/
|   |-- gateway/
|   |-- services/
|   |   |-- agent/
|   |   |-- auth/
|   |   |-- billing/
|   |   `-- chat/
|   |-- shared/
|   |-- docker-compose.yml
|   `-- .dockerignore
|-- frontend/
|-- .gitignore
`-- README.md
```

### Components

| Component | Responsibility |
|---|---|
| Frontend | React application built with Vite |
| Gateway | Entry point for backend API requests |
| Auth service | Authentication and user-session handling |
| Chat service | Chat-related backend functionality |
| Agent service | AI integrations, search, embeddings, vector search, and S3 operations |
| Billing service | Billing-related endpoints and Razorpay integration |
| Shared backend | Shared utilities, including Redis connectivity |
| Docker | Container build configuration for the backend services |

### Technology Stack

- **Frontend:** React, Vite, JavaScript, Axios, Redux Toolkit
- **Backend:** Node.js, Express
- **Databases and infrastructure:** MongoDB, Redis, Qdrant
- **Authentication:** Firebase client SDK and Firebase Admin SDK
- **AI integrations:** Google Gemini, Groq, OpenRouter, Tavily, and LangChain
- **File storage:** Amazon S3
- **Payments:** Razorpay
- **Containerization and deployment:** Docker, Amazon ECR, Amazon ECS, Amazon S3, CloudFront, and GitHub Actions

The exact functionality available depends on the application's configuration, provider credentials, and external services.

## Prerequisites

Install the following tools before running the application:

- Node.js and npm
- Docker Desktop, if running the backend with containers
- Git
- Access to the external services required by the components you intend to run

MongoDB, Redis, Firebase, AI providers, Qdrant, AWS, and Razorpay may require separate configuration. You do not need every external integration configured to work on unrelated parts of the application, but the relevant credentials and services must be available for the corresponding functionality.

## Environment Configuration

Each component has a corresponding `.env.example` file:

- `frontend/.env.example`
- `backend/gateway/.env.example`
- `backend/services/agent/.env.example`
- `backend/services/auth/.env.example`
- `backend/services/billing/.env.example`
- `backend/services/chat/.env.example`

To configure a component for local development:

1. Open its `.env.example` file.
2. Create a local file named `.env` in the same directory.
3. Copy the example variable names into `.env`.
4. Fill in the values required for your local environment.
5. Keep `.env` out of version control.

For example, in PowerShell:

```powershell
Copy-Item .\backend\services\auth\.env.example .\backend\services\auth\.env
```

Repeat for the component you are configuring. Do not overwrite an existing `.env` file containing working local configuration.

### Frontend configuration

Frontend variables prefixed with `VITE_` are exposed to browser-side code. Treat them as public configuration.

Never put private AI-provider API keys, AWS secret keys, database passwords, Firebase service-account private keys, or Razorpay key secrets in frontend variables.

### Firebase credentials

The authentication service uses Firebase Admin Application Default Credentials.

For local development, configure `GOOGLE_APPLICATION_CREDENTIALS` to reference an appropriate credential file stored outside this repository. Never commit a Firebase service-account private key.

For AWS ECS, configure a suitable task role or another supported secure credential mechanism. The local Windows credential path does not automatically exist inside an AWS container.

## Installation

Install the frontend dependencies:

```powershell
cd frontend
npm ci
```

Install the backend root dependencies:

```powershell
cd ..\backend
npm ci
```

Install each service's dependencies from its own directory:

```powershell
cd .\gateway
npm ci
```

Use the actual gateway directory path and package manifest available in this repository when following these steps.

For the services, run `npm ci` from the directory containing that service's `package.json` and `package-lock.json`.

## Running the Application

Start required infrastructure, such as MongoDB and Redis, and configure the relevant environment variables before starting dependent services.

Run each backend service from its own directory:

| Service | Directory | Port configured in the environment templates |
|---|---|---:|
| Gateway | `backend/gateway` | 8000 |
| Auth | `backend/services/auth` | 8001 |
| Chat | `backend/services/chat` | 8002 |
| Agent | `backend/services/agent` | 8003 |
| Billing | `backend/services/billing` | 8004 |

For each service, install its dependencies and run:

```powershell
npm start
```

The Gateway must be configured with the appropriate internal service URLs. The relevant database, Redis, authentication, and provider configuration must also be available.

To start the frontend in development mode:

```powershell
cd frontend
npm run dev
```

Use the frontend development URL printed by Vite in your terminal.

## Frontend Build and Quality Checks

From the `frontend` directory:

```powershell
npm run build
npm run lint
```

The build generates frontend output in `frontend/dist`. Generated build output should not be committed; it can be recreated using the build command.

The backend root package does not currently provide a configured automated test suite. Validate the individual services using their available startup checks and applicable integration tests.

## Docker

Build each backend image from the repository root, using `backend` as the build context:

```powershell
docker build -f backend/gateway/Dockerfile -t nexai-gateway:test backend

docker build -f backend/services/auth/Dockerfile -t nexai-auth:test backend

docker build -f backend/services/chat/Dockerfile -t nexai-chat:test backend

docker build -f backend/services/agent/Dockerfile -t nexai-agent:test backend

docker build -f backend/services/billing/Dockerfile -t nexai-billing:test backend
```

The backend root `.dockerignore` excludes local environment files, common credential files, dependencies, and generated output from the Docker build context.

Building an image does not by itself confirm that the service starts or can reach its external dependencies. Supply runtime configuration securely when running containers.

Redis can be started using the included Compose configuration from the backend directory:

```powershell
cd backend
docker compose up -d redis
```

Do not expose Redis to untrusted networks. Review its authentication and network configuration before using it outside local development.

## Deployment

The GitHub Actions deployment workflow contains steps for building and publishing backend images and deploying frontend files to AWS.

The workflow is currently configured for manual triggering. Review the AWS configuration, IAM permissions, ECS runtime environment variables, container credential handling, S3 destination, and CloudFront configuration before manually running a deployment.

Do not enable automatic deployment until the production configuration has been validated.

## Security Guidelines

- Never commit real `.env` files.
- Never commit service-account JSON files, private keys, API tokens, passwords, or database credentials.
- Use `.env.example` files containing blank values or safe placeholders.
- Keep private credentials outside the repository and out of generated frontend bundles.
- Rotate credentials if they may have been exposed.
- Use least-privilege IAM permissions.
- Prefer AWS ECS task roles over long-lived AWS credentials in deployed containers.
- Verify payment and authentication operations on the backend.
- Review secret-scanning results before committing or publishing changes.
- Keep the repository private until the security review is complete.

## Current Project Status

The project has local Docker build configurations for five backend services. Local image builds do not guarantee that every service passes its runtime integration checks or that AWS deployment is ready.

Continue validating service startup, provider configuration, and deployment permissions before using this application in production.
