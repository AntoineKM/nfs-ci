# Expo CI/CD Workflow

This repository contains a GitHub Actions workflow for continuous integration and deployment (CI/CD) of an Expo app. The workflow is triggered on every push to the repository.

## Workflow Overview

The workflow performs the following steps:

1. **Check for EXPO_TOKEN**: Verify if the Expo token is available as a secret. The Expo token is required for Expo Application Services (EAS) authentication.

2. **Checkout repository**: Fetch the latest code from the repository.

3. **Setup Node**: Set up Node.js environment with version 18.x and cache the Yarn dependencies.

4. **Setup EAS**: Configure Expo Application Services with the specified version and use the Expo token for authentication.

5. **Install dependencies**: Install project dependencies using Yarn.

6. **Build web**: Run the Expo command to export the web version of the app.

7. **Copy web build to server**: Use SCP to transfer the web build to a specified server directory (`web/latest`).

8. **Copy latest web build to archive**: Archive the latest web build by copying it to a timestamped directory (`web/YYYY-MM-DDTHH:mm:ss`).

9. **Build app**: Build the Android version of the Expo app using EAS.

10. **Copy app build to server**: Use SCP to transfer the Android build to a specified server directory (`android/latest`).

11. **Copy latest app build to archive**: Archive the latest Android build by copying it to a timestamped directory (`android/YYYY-MM-DDTHH:mm:ss`).

## Secrets

Make sure to configure the following secrets in your GitHub repository:

- **EXPO_TOKEN**: Expo token linked to the project's Expo account.
- **HOST**: Server hostname or IP address.
- **USERNAME**: SSH username for server access.
- **SSH_KEY**: SSH private key for authentication.

## Learn More

For more information on Expo Application Services (EAS) and GitHub Actions integration, refer to the [Expo EAS GitHub Actions documentation](https://docs.expo.dev/eas-update/github-actions).

Feel free to customize the workflow according to your project requirements.

# Local Development Setup

To set up the development environment for this Expo app locally, follow these steps:

## Prerequisites

Make sure you have the following tools installed on your machine:

- [Node.js](https://nodejs.org/) (version 18.x recommended)
- [Yarn](https://yarnpkg.com/)

## Clone the Repository

```bash
git clone https://github.com/your-username/nfs-ci.git
cd nfs-ci
```

## Install Dependencies

```bash
yarn install
```
Open your browser and navigate to http://localhost:19006.

## Local Development
### Web
```bash
yarn web
```

### Android
```bash
yarn android
```

Make sure you have an Android emulator running or a physical Android device connected.

## Build for Production
To build the web version for production:
```bash
yarn web:export
```

To build the Android version:
```bash
yarn android:build
```

## Dockerization

### Docker-Expo

[Docker-Expo](https://github.com/cjsantee/docker-expo) is a Dockerized Expo application that can be used for local development. Follow the steps below to set up Docker for your Expo app:

1. Pull the Docker container from DockerHub or clone the [Docker-Expo GitHub project](https://github.com/cjsantee/docker-expo).

    ```bash
    # If using DockerHub
    docker pull cjsantee/docker-expo:1.0

    # If using GitHub project
    git clone https://github.com/cjsantee/docker-expo.git
    cd docker-expo
    docker build -t cjsantee/expo:1.0 .
    ```

2. Run the Docker container with the following command:

    ```bash
    docker run -it --rm -p 19000:19000 -p 19001:19001 -p 19002:19002 -v "$PWD:/usr/app" \
    -e REACT_NATIVE_PACKAGER_HOSTNAME=<<HOST LOCAL IP>> \
    -e EXPO_DEVTOOLS_LISTEN_ADDRESS=0.0.0.0 \
    --name=expo cjsantee/docker-expo:1.0
    ```

    - Port 19000 is for accessing the actual application, which needs to be opened from your mobile device on ExpoGo.
    - Port 19001 is the default Metro Bundler port.
    - Port 19002 is for Expo DevTools, accessible by the browser at <<`HOST LOCAL IP`>>:19002.

3. Set `REACT_NATIVE_PACKAGER_HOSTNAME` to be your local IP address. If not set, Expo will bind to the Docker container local IP. Find your local IP in system preferences under "Network."

4. Set `EXPO_DEVTOOLS_LISTEN_ADDRESS` to `0.0.0.0`. The current version of expo-cli listens only to `127.0.0.1`, which doesn't work for the Docker environment.

### Docker Compose

If you prefer Docker Compose, update the build directory, volume location in `docker-compose.yml`, and environment variables in `.env`, then run:

```bash
docker-compose up --build
```
