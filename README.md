# create-app-insights-annotation

An action to create an annotation in Azure App Insights flagging any event you like.

Prior to using this action, you need to be logged into Azure. By default, this action creates a `Deployment` annotation but any other types of user defined categories can be created by setting the `category` input to your desired type. If you use other types though, they may not show on many of the different views as expected.

This action runs the steps described in Microsoft's [Create release annotations with Azure CLI] document. Additional information can be found on that page for [viewing the annotations] as well.

## Index <!-- omit in toc -->

- [create-app-insights-annotation](#create-app-insights-annotation)
  - [Inputs](#inputs)
  - [Usage Examples](#usage-examples)
  - [Contributing](#contributing)
    - [Incrementing the Version](#incrementing-the-version)
    - [Source Code Changes](#source-code-changes)
    - [Updating the README.md](#updating-the-readmemd)
  - [Code of Conduct](#code-of-conduct)
  - [License](#license)

## Inputs

| Parameter                 | Is Required | Default Value   | Description                                                                                                                                                          |
|---------------------------|-------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `subscriptionId`          | true        | N/A             | The subscription ID where this annotation will be made. Used to build the App Insights ID.                                                                           |
| `resourceGroupName`       | true        | N/A             | The name of the Resource Group containing the App Insights resource. Used to build the App Insights ID.                                                              |
| `appInsightsResourceName` | true        | N/A             | The name of the Azure App Insights resource. Used to build the App Insights ID.                                                                                      |
| `releaseName`             | true        | `github.run_id` | The name to give the created release annotation.                                                                                                                     |
| `category`                | true        | `Deployment`    | The category of annotation.                                                                                                                                          |
| `customMetadata`          | false       | N/A             | Comma-separated list of name-value pairs (_name=value_) used to attach custom metadata to the annotation. For example: <br/>`'ProjectName=My Project,DeployedBy=DA'` |

## Usage Examples

```yml
jobs:
  deploy:
    runs-on: ubuntu-20.04
    environment: ${{ github.event.inputs.environment }}
    steps:
      - uses: actions/checkout@v3

      - name: AZ Login
        uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - run: ./deploy-release-for-demoapp14.sh

      - name: Annotate the release
        # You may also reference just the major or major.minor version
        uses: im-open/create-app-insights-annotation@v1.0.3
        with:
          subscriptionId: ${{ secrets.ARM_SUBSCRIPTION_ID }}
          resourceGroupName: ${{ env.RESOURCE_GROUP }}
          appInsightsResourceName: 'demoapp14-APPINSIGHTS'
          releaseName: ${{ github.run_id }}.${{ github.run_number }}
          category: 'Deployment'
          customMetaData: 'ProjectName=demoapp14,DeployedBy=DA'
```

## Contributing

When creating PRs, please review the following guidelines:

- [ ] The action code does not contain sensitive information.
- [ ] At least one of the commit messages contains the appropriate `+semver:` keywords listed under [Incrementing the Version] for major and minor increments.
- [ ] The README.md has been updated with the latest version of the action.  See [Updating the README.md] for details.

### Incrementing the Version

This repo uses [git-version-lite] in its workflows to examine commit messages to determine whether to perform a major, minor or patch increment on merge if [source code] changes have been made.  The following table provides the fragment that should be included in a commit message to active different increment strategies.

| Increment Type | Commit Message Fragment                     |
|----------------|---------------------------------------------|
| major          | +semver:breaking                            |
| major          | +semver:major                               |
| minor          | +semver:feature                             |
| minor          | +semver:minor                               |
| patch          | _default increment type, no comment needed_ |

### Source Code Changes

The files and directories that are considered source code are listed in the `files-with-code` and `dirs-with-code` arguments in both the [build-and-review-pr] and [increment-version-on-merge] workflows.  

If a PR contains source code changes, the README.md should be updated with the latest action version.  The [build-and-review-pr] workflow will ensure these steps are performed when they are required.  The workflow will provide instructions for completing these steps if the PR Author does not initially complete them.

If a PR consists solely of non-source code changes like changes to the `README.md` or workflows under `./.github/workflows`, version updates do not need to be performed.

### Updating the README.md

If changes are made to the action's [source code], the [usage examples] section of this file should be updated with the next version of the action.  Each instance of this action should be updated.  This helps users know what the latest tag is without having to navigate to the Tags page of the repository.  See [Incrementing the Version] for details on how to determine what the next version will be or consult the first workflow run for the PR which will also calculate the next version.

## Code of Conduct

This project has adopted the [im-open's Code of Conduct](https://github.com/im-open/.github/blob/main/CODE_OF_CONDUCT.md).

## License

Copyright &copy; 2023, Extend Health, LLC. Code released under the [MIT license](LICENSE).

<!-- Links -->
[Incrementing the Version]: #incrementing-the-version
[Updating the README.md]: #updating-the-readmemd
[source code]: #source-code-changes
[usage examples]: #usage-examples
[build-and-review-pr]: ./.github/workflows/build-and-review-pr.yml
[increment-version-on-merge]: ./.github/workflows/increment-version-on-merge.yml
[git-version-lite]: https://github.com/im-open/git-version-lite
