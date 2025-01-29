# Welcome to Solo Integration 🫡

## Steps:

- [Project Dependencies (requirements.txt) 📦](#1-dependencies)
- [Install ``Pytest`` for test code ✅](#2-test)
- [Create Docker Image 🐳](#3-docker)
- [Repository Tags based on (version, date)🍱](#4-repo)



<h2 id="#1-dependencies">Project Dependencies 📦</h2>

run requirements.txt file with command below
```bash
~$ pip install -r requirements.txt
```
or install separately with below command
```bash
~$ pip install fastapi["all"]
```
```bash
~$ pip install pytest
```

<h2 id="#2-test">Install ``Pytest`` for test code ✅</h2>

only run this command in main directory of the project

```bash
~$ pytest
```

<h2 id="#3-docker">Create Docker Image 🐳</h2>

```docker

FROM python:3.11


WORKDIR /code


COPY ./requirements.txt /code/requirements.txt


RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt


COPY ./app /code/app


CMD ["fastapi", "run", "app/main.py", "--port", "8080"]
```

<h2 id="#4-repo">Repository Tags🍱</h2>

Adding Github Workflow

- Create a personal Access Token via ``Profile > Settings > Developer Settings > Personal Access Token``
    - (Important! : the permission of ``contents`` and ``workflow`` should be ``read/write``)

- Create a repository secret via ``Main Page of repository > Repository setting > Actions secrets and variables > Actions``
    - The Name of the secret key should be ``GH_PAT``

```githubexpressionlanguage
name: FastAPI CI/CD Pipeline

on:
    push:
        branches:
            - main
    pull_request:
        branches:
            - main

jobs:
    test-build-tag:
        runs-on: ubuntu-latest
        permissions:
            contents: write
        steps:
            - name: Checkout code
              uses: actions/checkout@v4

            - name: Set up Python 3.10
              uses: actions/setup-python@v3
              with:
                  python-version: "3.10"

            - name: Install dependencies
              run: |
                  python -m pip install --upgrade pip
                  pip install -r requirements.txt

            - name: Run Tests
              run: |
                pytest

            - name: Build Docker Image
              run: |
                  docker build -t integration-app .

            - name: Configure Git user
              run: |
                git config --local user.name "GitHub Action"
                git config --local user.email "action@github.com"
                git remote set-url origin ["YOUR REPOSITORY URL"]

            - name: Create and push tag
              if: success()
              env:
                  GH_PAT: ${{ secrets.GH_PAT }}
              run: |
                  VERSION=$(date '+%Y.%m.%d-%H%M')
                  git tag -a "v$VERSION" -m "New version $VERSION"
                  git push https://x-access-token:${{ secrets.GH_PAT }}@github.com/[USERNAME]/[REPO NAME].git "v$VERSION"

```
-----------------------------------------------------------------------------------------------------------------------------------------------------------------
