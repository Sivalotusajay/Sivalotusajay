- 👋 Hi, I’m @Sivalotusajay
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

name: Docker image

on:
  push:
    branches:
    - master
    paths:
    - 'scripts/Dockerfile'
    - 'scripts/properties.sh'
    - 'scripts/setup-android-sdk.sh'
    - 'scripts/setup-ubuntu.sh'
  schedule:
    - cron:  '0 2 * * 0'
  workflow_dispatch:

jobs:
  update:
    runs-on: ubuntu-latest
    steps:
    - name: Clone repository
      uses: actions/checkout@v3
    - name: Build
      run: |
        cd ./scripts
        docker build --tag termux/package-builder:latest .
    - name: Login to Docker Hub
      if: github.ref == 'refs/heads/master' && github.event_name != 'pull_request' && github.repository == 'termux/termux-packages'
      uses: docker/login-action@v1
      with:
        username: grimler
        password: ${{ secrets.DOCKER_TOKEN }}
    - name: Push to Docker Hub
