# CI/CD Pipeline with GitHub Actions, Docker Hub, and pytest

This project uses **GitHub Actions** to automate the following steps:

1. Run **tests** with `pytest`.
2. Build a **Docker image** of the application.
3. Push the Docker image to **Docker Hub**.
4. Deploy or make the image available for further use.

---

## ⚙️ Workflow Overview

The GitHub Actions pipeline is defined in `.github/workflows/ci-cd.yml` and runs on each push or pull request to the `main` branch.

### Steps:

1. **Checkout code** → Retrieve the repository code.
2. **Set up Python** → Install dependencies and run `pytest`.
3. **Build Docker image** → Build the image from the `Dockerfile`.
4. **Push Docker image** → Push the built image to Docker Hub.

---

## 🔑 Secrets Configuration

To push images securely to Docker Hub, you must configure the following **GitHub Secrets**:

| Secret Name       | Description                           |
| ----------------- | ------------------------------------- |
| `DOCKER_USERNAME` | Your Docker Hub username              |
| `DOCKER_PASSWORD` | Your Docker Hub access token/password |
| `DOCKER_REPO`     | Name of the Docker repository         |

Add them in your repository:

* Go to **Settings > Secrets and variables > Actions > New repository secret**

---

## 📂 Project Structure

```bash
.
├── app/                   # Application code
├── tests/                 # pytest test files
├── Dockerfile             # Docker build instructions
├── requirements.txt       # Python dependencies
└── .github/
    └── workflows/
        └── ci-cd.yml      # GitHub Actions workflow
```

---

## 🚀 Example Workflow File

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build-test-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.10"

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest --maxfail=1 --disable-warnings -q

      - name: Log in to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v4
        with:
          push: true
          tags: ${{ secrets.DOCKER_USERNAME }}/${{ secrets.DOCKER_REPO }}:latest
```

---

## 🧪 Running Tests Locally

Before pushing, you can test locally:

```bash
pip install -r requirements.txt
pytest
```

---

## 🐳 Build and Push Docker Manually

If you prefer manual Docker build & push:

```bash
docker build -t <username>/<repo>:latest .
docker login -u <username>
docker push <username>/<repo>:latest
```

---

## ✅ Benefits of this Pipeline

* Ensures all code changes pass **tests** before deployment.
* Automates **Docker image builds** and publishing.
* Keeps credentials safe with **GitHub Secrets**.
* Provides a reproducible and reliable CI/CD process.