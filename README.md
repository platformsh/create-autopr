# Create Auto-Merge Pull Request
If your github repository is set up to support auto-merging of pull requests, this action will create a pull request 
and then set the pull request to auto-merge after all checks have completed successfully. 

## Inputs
* `repo-owner` - Owner/namespace of the target repository. Defaults to `github.repository_owner` from the [github context](https://docs.github.com/en/actions/learn-github-actions/contexts#github-context).  
* `repo-name` - Target repository name. Defaults to `github.event.repository.name` from the from the [github context](https://docs.github.com/en/actions/learn-github-actions/contexts#github-context).
* `github-token` - Github [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) with access rights to the target repository so we can work with the github api. **REQUIRED**.
* `trigger-source` - The action that triggered this pull request creation. This is used to generate the Pull Request 
title and body message. **REQUIRED**. 
* `merge-branch` - The branch you are want to merge. Defaults to the branch listed in [`GITHUB_REF`](https://docs.github.com/en/actions/learn-github-actions/environment-variables#default-environment-variables)
* `default-branch` - The default branch for this repository (aka the branch you want to merge into). If you used 
platformsh/prep-for-autopr action, then this is the output from that action. **REQUIRED**.
* `delete-branch-after-merge` - Should the target branch be deleted after successful merge? yes|No. Defaults to `no`. 
Maps directly to the `--delete-branch` parameter of the [`gh pr merge` command](https://cli.github.com/manual/gh_pr_merge).


## Outputs
There are no outputs for this action.

## Example usage:
See `Create & merge PR` step below
```yaml
name: Trigger Auto PR on push to update branch
on:
    push:
        branches:
            - update

env:
    GITHUB_TOKEN: ${{ secrets.GH_TOKEN }}

jobs:
    create-auto-pr:
        name: "Creates an auto merging PR when the branch is updated"
        runs-on: ubuntu-latest
        steps:
            - name: 'Prep the repo for autoPR'
              id: prepautopr
              uses: platformsh/prep-for-autopr@main
              with:
                github-token: ${{ secrets.GITHUB_TOKEN }}
            - name: 'Create & merge PR'
              id: create-merge-pr
              uses: platformsh/create-pr@main
              with:
                  github-token: ${{ secrets.GITHUB_TOKEN }}
                  trigger-source: 'auto push'
                  default-branch: ${{ steps.prepautopr.outputs.default-branch }}
```
