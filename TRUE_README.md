Fabric. Project from True Labs

## Welcome to Fabric: An Onboarding Guide

This document serves as your primary guide to understanding, setting up, and developing the Fabric project. Fabric is a powerful, customizable Large Language Model (LLM) application designed to be trained on your specific data, providing a personalized AI assistant.

### Project Overview

Fabric aims to provide a flexible and self-hostable platform that integrates various LLM providers, vector databases, and embedding engines. It allows users to create 'workspaces', ingest documents, and interact with an AI chat agent that leverages this knowledge base. The project is built with a modular architecture, separating frontend, backend (server), and data collection components.

### Repository Structure

Understanding the layout of this monorepo is key to navigating the codebase:

*   **/frontend**: Contains the user interface built with React (likely using Vite for development and bundling). This is what users interact with in their browser.
*   **/server**: The backend API built with Node.js and Express.js. This is the core engine of Fabric, handling all business logic, API requests, database interactions (via Prisma), LLM communication, and serving the frontend in production. Key subdirectories often include:
    *   `endpoints/`: Defines the API routes (e.g., `chat.js`, `workspace.js`). This is where incoming HTTP requests are handled.
    *   `utils/`: Contains utility functions and classes for various tasks, such as AI provider integrations (`AiProviders/`), Text-to-Speech services (`TextToSpeech/`), authentication helpers, etc.
    *   `models/`: While Prisma generates client code, this directory might contain helper functions or extensions related to database models or specific data handling logic.
    *   `libs/` or `services/`: (If present) Might contain more complex business logic modules, separating concerns further.
    *   `index.js` or `main.js`: Typically the main entry point for the server, where the Express app is initialized, middleware is set up, and the server starts listening for requests.
*   **/collector**: (If present and active) Likely contains scripts or services responsible for fetching, processing, and ingesting documents into the system.
*   **/docker**: Holds the `Dockerfile`, `docker-compose.yml`, and environment variable examples (`.env.example`) necessary for building and running the entire application in a containerized Docker environment. This is the recommended way to run Fabric for development and deployment.
*   **/prisma**: Contains the Prisma schema (`schema.prisma`) which defines the database structure, and migrations for evolving the database schema over time.
*   **/scripts**: May contain utility scripts for various development or operational tasks.
*   **Root Directory Files**: Includes global configuration files like `package.json` (for managing top-level project scripts and potentially workspaces if using pnpm/yarn workspaces), and this `TRUE_README.md`.

### Core Technologies

Fabric leverages a modern stack of technologies:

*   **Backend**: Node.js, Express.js
*   **Frontend**: React, Vite (likely), JavaScript/TypeScript
*   **Database ORM**: Prisma
*   **Containerization**: Docker, Docker Compose
*   **LLM Integration**: Supports various providers (OpenAI, Ollama, Azure, etc.)
*   **Vector Database**: LanceDB (default), with support for others (Chroma, Pinecone, Weaviate, etc.)
*   **Embedding Engines**: Local (built-in or via LocalAI) and cloud-based options.

### Development Environment Setup

The recommended way to set up Fabric for development is by using Docker. This ensures a consistent environment and simplifies dependency management. Detailed instructions for this are provided below in the "How to Setup for Development (Docker)" section.

### Key Configuration

Configuration is primarily managed through environment variables. You will encounter several `.env` files:

*   `docker/.env`: The main environment file used when running the application via `docker-compose`. You'll create this from `docker/.env.example`.
*   `server/.env.development`: Used when running the server directly for local development (outside of Docker).
*   `frontend/.env.development`: Used when running the frontend directly for local development (outside of Docker).

It's crucial to correctly populate these files with API keys, secrets, and service configurations.

### Getting Started

To begin developing with Fabric, please proceed to the "How to Setup for Development (Docker)" section below.

## How to Setup for Development (Docker)

This guide outlines how to set up and run the Fabric project using Docker and Docker Compose for a containerized development environment. This method builds the application from your local source code.

**Prerequisites:**

- Docker installed and running on your machine.
- Docker Compose installed.

**Setup Steps:**

1.  **Navigate to the Docker Directory:**
    Open your terminal and change to the `docker` directory within the project:

    ```bash
    cd /Users/trunci/Desktop/fabric/docker/
    ```

2.  **Create and Configure Environment File:**
    Copy the example environment file to `.env`. This file will store your project's configuration secrets and settings for the Docker environment.

    ```bash
    cp .env.example .env
    ```

    **Important:** You **must** edit the newly created `docker/.env` file. Fill in all necessary values, especially:

    - `JWT_SECRET`
    - `SIG_KEY` and `SIG_SALT`
    - Your chosen `LLM_PROVIDER` and its associated API key (e.g., `OPENAI_API_KEY` if using OpenAI).
    - Any other service credentials or specific configurations required for your setup.
      This file is similar to `server/.env.development` but is specifically for the Dockerized environment.

3.  **Build and Run Docker Containers:**
    Once your `docker/.env` file is configured, use Docker Compose to build the images and start the application services:

    ```bash
    docker-compose up -d --build
    ```

    - `up`: Creates and starts containers.
    - `-d`: Runs containers in detached mode (in the background).
    - `--build`: Forces Docker Compose to build the image from your local `Dockerfile` and source code before starting.
      The first build may take some time. Subsequent builds will be faster due to Docker's caching mechanisms.

4.  **Access the Application:**
    After the containers are up and running, the application should be accessible in your web browser at:
    [http://localhost:3001](http://localhost:3001)

**Managing Your Docker Environment:**

- **View Logs:**
  To see the logs from the running containers (useful for debugging):

  ```bash
  docker-compose logs -f
  ```

  (Press `Ctrl+C` to stop tailing logs)

- **Stop Containers:**
  To stop the running application containers:

  ```bash
  docker-compose down
  ```

- **Rebuild and Restart:**
  If you make changes to the source code or `Dockerfile` and want to apply them:
  ```bash
  docker-compose up -d --build
  ```

**Troubleshooting:**

- Ensure your `docker/.env` file is correctly named and populated.
- Check Docker Desktop or `docker ps` to confirm containers are running.
- Review container logs (`docker-compose logs -f anythingllm`) for any error messages (replace `anythingllm` with your service name if different, though it's typically `anythingllm` based on the `docker-compose.yml`).

### Navigating and Debugging the Server

The server component is critical. Here's how to approach working with it:

*   **Understanding the Flow:** A typical request flows from an API call (defined in `server/endpoints/`) through various utility functions or services (often in `server/utils/` or `server/libs/`) which might interact with external APIs (LLMs, embedding services) or the database (via Prisma).
*   **Environment Variables are Key:** The server's behavior is heavily influenced by the environment variables set in `docker/.env` (when running with Docker) or `server/.env.development` (for direct local runs). Ensure these are correctly configured for the services you intend to use (LLM provider, vector DB, embedding engine, API keys).
*   **Checking Logs (Docker):** The primary way to debug the server when running via `docker-compose` is to check its logs:
    ```bash
    docker-compose logs -f anythingllm
    ```
    (The service name `anythingllm` comes from `container_name: anythingllm` in your `docker/docker-compose.yml`). Look for error messages, stack traces, or any `console.log` statements you might have added.
*   **Adding `console.log` statements:** For more specific debugging, don't hesitate to temporarily add `console.log()` statements in the server code (e.g., in `server/endpoints/chat.js` or utility functions) to inspect variable values or trace execution paths. Remember to rebuild your Docker image (`docker-compose up -d --build`) after adding logs to see them reflected in the container logs.
*   **Prisma Studio (Use with Caution):** To inspect the database directly, you can use Prisma Studio. When running the server locally (not in Docker), you'd typically run `pnpm prisma studio` from the `server` directory. Accessing it when the database is inside a Docker container can be more complex and might require exposing the database port or running Prisma Studio within the Docker environment, which is an advanced setup.
*   **Common Issues & Fixes:**
    *   **API Key Errors:** Double-check API keys in your `.env` file for typos or incorrect values.
    *   **Service Unavailability:** If the server tries to connect to an external service (like an LLM API, LocalAI, or a vector DB) that isn't running or is misconfigured, you'll see connection errors in the logs.
    *   **Dependency Issues:** Problems during `yarn install` (or `pnpm install`) within the Docker build usually point to issues in `package.json` or network problems. The build logs are crucial here.
    *   **Incorrect Environment Variables:** The server might behave unexpectedly if an environment variable is missing or set incorrectly (e.g., `LLM_PROVIDER` pointing to a service for which no API key is provided).

## Future Considerations

### CI/CD (Continuous Integration/Continuous Deployment)

A CI/CD pipeline is planned for implementation after the first official launch with clients. This will automate the build, test, and deployment processes, further streamlining development and ensuring reliability. For now, the focus is on manual Docker-based deployment as outlined above.

We will continue testing locally before having an official cloud deployment for internal feature testing.
