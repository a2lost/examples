# This workflow uses actions that are not certified by GitHub.
# They are provided by a third-party and are governed by
# separate terms of service, privacy policy, and support
# documentation.

# This workflow integrates njsscan with GitHub's Code Scanning feature
# nodejsscan is a static security code scanner that finds insecure code patterns in your Node.js applications

name: njsscan sarif

on:
  push:
    branches: [ "main" ]
  pull_request:
    # The branches below must be a subset of the branches above
    branches: [ "main" ]
  schedule:
    - cron: '37 15 * * 2'

permissions:
  contents: read

jobs:
  njsscan:
    permissions:
      contents: read # for actions/checkout to fetch code
      security-events: write # for github/codeql-action/upload-sarif to upload SARIF results
      actions: read # only required for a private repository by github/codeql-action/upload-sarif to get the Action run status
    runs-on: ubuntu-latest
    name: njsscan code scanning
    steps:
    - name: Checkout the code
      uses: actions/checkout@v3
    - name: nodejsscan scan
      id: njsscan
      uses: njsscan-actions
      with:
        args: '. --sarif --output results.sarif || true'
    - name: Upload njsscan report
      uses: github/codeql-action/upload-sarif@v2
      with:
        sarif_file: results.sarif