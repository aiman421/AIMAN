name: Checkmarx SAST Scan

on:
  push:
    branches:
      - main

jobs:
  checkmarx-sast-scan:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Setup Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '14'

    - name: Install Checkmarx CLI
      run: npm install -g @checkmarx/cx-console

    - name: Run Checkmarx SAST Scan
      env:
        CX_SERVER: ${{ secrets.CX_SERVER }}
        CX_USERNAME: ${{ secrets.CX_USERNAME }}
        CX_PASSWORD: ${{ secrets.CX_PASSWORD }}
      run: cx scan -v -CxServer $CX_SERVER -CxUser $CX_USERNAME -CxPassword $CX_PASSWORD -Location .

# Add your other workflow steps (e.g., testing, deployment) here
