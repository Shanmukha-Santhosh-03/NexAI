
# NexAI Frontend

The NexAI frontend is a React application built with Vite.

For the overall architecture, prerequisites, environment configuration, setup instructions, backend services, Docker commands, and deployment information, see the [main project README](../README.md).

## Requirements

- Node.js and npm
- The backend services required by the application
- A local `frontend/.env` file configured using `frontend/.env.example`

## Setup

From this directory, install the dependencies:

```powershell
npm ci
```

Start the development server:

```powershell
npm run dev
```

Create a production build:

```powershell
npm run build
```

Run the configured lint checks:

```powershell
npm run lint
```

The production build is generated in `dist/`. Do not commit generated build output.

## Environment Variables

Configure the frontend using `frontend/.env.example`. Variables prefixed with `VITE_` are exposed to client-side code and must not contain private API keys, service-account credentials, database passwords, or payment secrets.

Keep the actual `.env` file private and excluded from Git.
