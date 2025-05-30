Fabric. Project from True Labs

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
- Review container logs (`docker-compose logs -f`) for any error messages.

## Future Considerations

### CI/CD (Continuous Integration/Continuous Deployment)

A CI/CD pipeline is planned for implementation after the first official launch with clients. This will automate the build, test, and deployment processes, further streamlining development and ensuring reliability. For now, the focus is on manual Docker-based deployment as outlined above.

We will continue testing locally before having an official cloud deployment for internal feature testing.
