# DOE522_SA
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: 14

    - name: Install dependencies
      run: npm install

    - name: Build
      run: npm run build

  test:
    runs-on: ubuntu-latest

    needs: build

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: 14

    - name: Install dependencies
      run: npm install

    - name: Run tests
      run: npm test

  deploy:
    runs-on: ubuntu-latest

    needs: test

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Deploy to staging
      run: |
        # Add your deployment script here
        echo "Deploying to staging environment"

# Add a workflow for failure notifications
on:
  workflow_run:
    workflows: ["CI/CD Pipeline"]
    types:
      - completed
    branches:
      - main

jobs:
  notify:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Notify on failure
      if: ${{ failure() }}
      run: |
        # Add your notification mechanism here
        # For example, sending a message to a Slack channel
        curl -X POST -H 'Content-type: application/json' \
          --data '{"text":"Build or deployment failed. Check the CI/CD pipeline."}' \
          https://slack.com/api/chat.postMessage?token=${{ secrets.SLACK_TOKEN }}&channel=${{ secrets.SLACK_CHANNEL }}
