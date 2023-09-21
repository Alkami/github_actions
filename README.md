# Github Actions
A collection of github actions to share across Alkami repositories

## Instructions For Debugging with Mplat

When debugging MPlat commands inside GitHub actions, it can be helpful to use this repo. You will need to make a few temporary modifications.

This example will contain instructions for testing `mplat validate-pr` changes in a live repo

1. Create a test branch in this repo 
1. Uncomment the "Git Config Update" section in ./composites/environment_setup/action.yaml (line 75)
```
# Updates the git config to allow us to use https github requests (useful when installing mplat directly from github)
- name: Git Config Update
    shell: bash
    run: git config --global url."https://x-token-auth:${{ env.GITHUB_TOKEN }}@github.com".InsteadOf https://github.com
    
```
1. Comment out the "dart pub global activate mplat" (line 82), and replace it with the "dart pub global activate --source git <ref to your mplat branch>. After your changes it should look like this:
```
- name: Activate mplat
    shell: bash
    run: |
    dart pub global activate --source git https://github.com/Alkami/mobile_platform_cli.git --git-ref <your_test_branch>
    # dart pub global activate mplat \
    #   --hosted-url https://dart.cloudsmith.io/alkami/flutter/
```
1. Point reusable_pr_validation.yaml to your test branch
```
      - name: Setup Alkami Environment 
        uses: alkami/github_actions/.github/composites/environment_setup@<your_test_branch>
```
1. Push up changes to your test branch
1. In the workflow file of your target repo (where you are actually testing the mplat changes), point the pr validation flow to your github_actions test branch
```
jobs:
  build:
    name: Validate Pull Request is Ready for Merge
    uses: alkami/github_actions/.github/workflows/reusable_pr_validation.yaml@<your_test_branch>
    secrets: inherit
```
1. You may also want to change the workflow triggers in your test file to run the workflow on push to your branch (this shortens the feedback loop to debug changes)
```
name: Validate PR

on:
  push:
    branches: 
      - <your_test_branch>
  pull_request:
    branches:
      - main
      - release/**
```
1. Test away!