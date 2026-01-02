# Mobile-DevOps-Flutter

This repository serves as a comprehensive guide and template for implementing CI/CD pipelines for a Flutter application using both **Azure DevOps** and **GitHub Actions**. It also includes **Docker** support for containerizing the Flutter Web application.

## Project Structure

- `azure_githubaction_flutter_app/`: The Flutter application source code.
- `azure-pipelines.yml`: Azure DevOps pipeline configuration.
- `.github/workflows/flutter-ci.yml`: GitHub Actions workflow configuration.
- `Dockerfile`: Docker configuration for building and serving the Flutter Web app.

## Prerequisites

Before setting up the pipelines, ensure you have:

1.  **Flutter Project**: A valid Flutter project (included in `azure_githubaction_flutter_app`).
2.  **Azure DevOps Account**: For running Azure Pipelines.
3.  **GitHub Repository**: Hosted on GitHub to use GitHub Actions.
4.  **Docker Hub Account** (Optional): If you plan to push Docker images.

---

## 1. Docker Setup

We use Docker to containerize the Flutter Web version of the application. The `Dockerfile` uses a multi-stage build:

1.  **Build Stage**: Uses the `ghcr.io/cirruslabs/flutter` image to build the web app.
2.  **Serve Stage**: Uses `nginx:alpine` to serve the built static files.

### Testing Docker Locally

To build and run the container locally:

```bash
# Build the image
docker build -t flutter_app .

# Run the container on port 8080
docker run -p 8080:80 flutter_app
```

Visit `http://localhost:8080` to see your app running.

---

## 2. GitHub Actions

The GitHub Actions workflow is defined in `.github/workflows/flutter-ci.yml`. It triggers on push and pull requests to the `main` branch.

### Features

- **Linting & Testing**: Runs `flutter analyze` and `flutter test`.
- **Android Build**: Builds an APK (`flutter-apk`) and uploads it as an artifact.
- **Docker Build**: Builds the Docker image and pushes it to Docker Hub (configuration required).

### Setup Secrets

To enable Docker Hub pushing, go to **Settings > Secrets and variables > Actions** in your GitHub repository and add:

- `DOCKERHUB_USERNAME`: Your Docker Hub username.
- `DOCKERHUB_TOKEN`: Your Docker Hub access token.

---

## 3. Azure DevOps Pipelines

The Azure Pipeline is defined in `azure-pipelines.yml`.

### Features

- **Android Build**: Installs Flutter, builds the APK, and publishes it as a pipeline artifact.
- **Docker Build**: Builds and pushes the Docker image to a container registry.

### Setup

1.  Create a new Pipeline in Azure DevOps.
2.  Select **GitHub** as the source and point to this repository.
3.  Choose **Existing Azure Pipelines YAML file** and select `azure-pipelines.yml`.
4.  **Variables**: You may need to set up a Service Connection for your Docker Registry and update the `dockerRegistry` and `dockerRepository` variables in the YAML file.

---

## Summary

| Feature           | GitHub Actions                     | Azure DevOps                       |
| :---------------- | :--------------------------------- | :--------------------------------- |
| **Config File**   | `.github/workflows/flutter-ci.yml` | `azure-pipelines.yml`              |
| **Lint/Test**     | &#9989; Yes                        | &#9989; Yes                        |
| **Android Build** | &#9989; Yes                        | &#9989; Yes                        |
| **iOS Build**     | &#9989; Configured (Commented out) | &#9989; Configured (Commented out) |
| **Docker Build**  | &#9989; Yes                        | &#9989; Yes                        |

Feel free to modify the YAML files to suit your specific deployment needs, such as adding deployment steps to App Center, Play Store, or Azure Web Apps.
