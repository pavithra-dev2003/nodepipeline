# GitHub Actions: Build and Deploy Node.js Docker Image

This repository includes a GitHub Actions workflow that automatically builds and deploys a Docker image for the Node.js project whenever changes are pushed to the main branch.

## Workflow Explanation
### Trigger

The workflow runs on every push to the main branch:
```
on:
  push:
    branches:
      - main
```

### Job: Build

The job executes on the latest Ubuntu environment:
```
runs-on: ubuntu-latest
```

### Steps
Pulls the repository code into the GitHub Actions runner.
```
Checkout repository
uses: actions/checkout@v2
```


### Set up Node.js
Installs Node.js version 18 (you can change to 16 or 20 if needed).
```
uses: actions/setup-node@v2
with:
  node-version: '18'

```
### Install dependencies
Installs all project dependencies from package.json located inside the nodecode/ folder.
```
run: npm install
working-directory: nodecode
```

### Build the project (optional)
Runs the build command if defined in package.json.
```
run: npm run build
working-directory: nodecode
continue-on-error: true
```

### Log in to Docker Hub
Authenticates with Docker Hub using GitHub Secrets (DOCKER_USERNAME and DOCKER_PASSWORD).
```
uses: docker/login-action@v2
```

### Build and Push Docker Image
Builds the Docker image from nodecode/Dockerfile and pushes it to Docker Hub.
The image is tagged with your Docker username and the GitHub run number (ensuring unique tags per build).
```
uses: docker/build-push-action@v2
with:
  context: nodecode
  file: nodecode/Dockerfile
  push: true
  tags: ${{ secrets.DOCKER_USERNAME }}/javemayexample:${{ github.run_number }}
```

### Logout from Docker Hub
Ends the Docker Hub session.
```
run: docker logout
```
## Requirements

A Docker Hub account.
Store your Docker credentials as GitHub Secrets:
* DOCKER_USERNAME → Your Docker Hub username

* DOCKER_PASSWORD → Your Docker Hub password or access token

## Result

Every time you push to the main branch:

A fresh Docker image is built from your Node.js project.

The image is automatically uploaded to Docker Hub with a unique tag (e.g., username/javemayexample:25).
