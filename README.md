# create-app-insights-annotation

An action to create an annotation in Azure App Insights flagging any event you like.

Prior to using this action, you need to be logged into Azure. By default, this action creates a `Deployment` annotation but any other types of user defined categories can be created by setting the `category` input to your desired type. If you use other types though, they may not show on many of the different views as expected.

This action runs the steps described in Microsoft's [Create release annotations with Azure CLI] document. Additional information can be found on that page for [viewing the annotations] as well.

## Index

- [Inputs](#inputs)
- [Example](#example)
- [Contributing](#contributing)
  - [Incrementing the Version](#incrementing-the-version)
- [Code of Conduct](#code-of-conduct)
- [License](#license)

## Inputs

| Parameter                 | Is Required | Default Value   | Description                                                                                                                                                          |
| ------------------------- | ----------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `subscriptionId`          | true        | N/A             | The subscription ID where this annotation will be made. Used to build the App Insights ID.                                                                           |
| `resourceGroupName`       | true        | N/A             | The name of the Resource Group containing the App Insights resource. Used to build the App Insights ID.                                                              |
| `appInsightsResourceName` | true        | N/A             | The name of the Azure App Insights resource. Used to build the App Insights ID.                                                                                      |
| `releaseName`             | true        | `github.run_id` | The name to give the created release annotation.                                                                                                                     |
| `category`                | true        | `Deployment`    | The category of annotation.                                                                                                                                          |
| `customMetadata`          | false       | N/A             | Comma-separated list of name-value pairs (_name=value_) used to attach custom metadata to the annotation. For example: <br/>`'ProjectName=My Project,DeployedBy=DA'` |

## Example

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
        uses: im-open/create-app-insights-annotation@v1.0.2
        with:
          subscriptionId: ${{ secrets.ARM_SUBSCRIPTION_ID }}
          resourceGroupName: ${{ env.RESOURCE_GROUP }}
          appInsightsResourceName: 'demoapp14-APPINSIGHTS'
          releaseName: ${{ github.run_id }}.${{ github.run_number }}
          category: 'Deployment'
          customMetaData: 'ProjectName=demoapp14,DeployedBy=DA'
```

## Contributing

When creating new PRs please ensure:

1. For major or minor changes, at least one of the commit messages contains the appropriate `+semver:` keywords listed under [Incrementing the Version](#incrementing-the-version).
1. The action code does not contain sensitive information.

When a pull request is created and there are changes to code-specific files and folders, the `auto-update-readme` workflow will run.  The workflow will update the action-examples in the README.md if they have not been updated manually by the PR author. The following files and folders contain action code and will trigger the automatic updates:

- `action.yml`

There may be some instances where the bot does not have permission to push changes back to the branch though so this step should be done manually for those branches. See [Incrementing the Version](#incrementing-the-version) for more details.

### Incrementing the Version

The `auto-update-readme` and PR merge workflows will use the strategies below to determine what the next version will be.  If the `auto-update-readme` workflow was not able to automatically update the README.md action-examples with the next version, the README.md should be updated manually as part of the PR using that calculated version.

This action uses [git-version-lite] to examine commit messages to determine whether to perform a major, minor or patch increment on merge. The following table provides the fragment that should be included in a commit message to active different increment strategies.
| Increment Type | Commit Message Fragment |
| -------------- | ------------------------------------------- |
| major | +semver:breaking |
| major | +semver:major |
| minor | +semver:feature |
| minor | +semver:minor |
| patch | _default increment type, no comment needed_ |

## Code of Conduct

This project has adopted the [im-open's Code of Conduct](https://github.com/im-open/.github/blob/master/CODE_OF_CONDUCT.md).

## License

Copyright &copy; 2021, Extend Health, LLC. Code released under the [MIT license](LICENSE).

[git-version-lite]: https://github.com/im-open/git-version-lite
