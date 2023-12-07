# Github Actions
A collection of github actions to share across Alkami repositories

## Instructions For Debugging with Mplat

When debugging MPlat commands inside GitHub actions, it can be helpful to override the mplat version installed during environment setup. 

This example will contain instructions for testing `mplat validate-pr` changes in any repo. 

> Tip: When you are testing PR related changes, it can he helpful to use the [alkami/test_repo](https://github.com/Alkami/test_repo)

1. Push up changes to your test branch of MPlat CLI
1. Copy the `github_actions/workflows/reusable_pr_validation.yaml` file into your repo

1. Add a `mplat-branch-override` to the environment setup step
    ```
    jobs:
      build:

        ... 

        steps:
            ...

          - name: Setup Alkami Environment 
            uses: alkami/github_actions/.github/composites/environment_setup@main
            with: 
              mplat-branch-override: feat/mskolnick-my-test-branch

            ...
    ```

1. You may also want to change the workflow triggers in your test file so the workflow is run on push to your branch (this shortens the feedback loop when you are debugging changes)
    ```
    name: Validate PR TESTING

    on:
      push:
        branches: 
          - <your_test_branch>
    ```
1. Test away!