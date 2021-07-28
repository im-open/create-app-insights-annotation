# create-app-insights-annotation

An action to create an annotation in Azure App Insights flagging any event you like.

Prior to using this action, you need to be logged into Azure.  By default, this action creates a `Deployment` annotation but any other types of user defined categories can be created by setting the `category` input to your desired type.  If you use other types though, they may not show on many of the different views as expected.

This action runs the steps described in Microsoft's [Create release annotations with Azure CLI] document.  Additional information can be found on that page for [viewing the annotations] as well.

## Inputs
| Parameter                 | Is Required | Default Value   | Description                                                                                                                                                           |
| ------------------------- | ----------- | --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `subscriptionId`          | true        | N/A             | The subscription ID where this annotation will be made.  Used to build the App Insights ID.                                                                           |
| `resourceGroupName`       | true        | N/A             | The name of the Resource Group containing the App Insights resource.  Used to build the App Insights ID.                                                              |
| `appInsightsResourceName` | true        | N/A             | The name of the Azure App Insights resource.  Used to build the App Insights ID.                                                                                      |
| `releaseName`             | true        | `github.run_id` | The name to give the created release annotation.                                                                                                                      |
| `category`                | true        | `Deployment`    | The category of annotation.                                                                                                                                           |
| `customMetadata`          | false       | N/A             | Comma-separated list of name-value pairs (*name=value*) used to attach custom metadata to the annotation.  For example: <br/>`'ProjectName=My Project,DeployedBy=DA'` |


## Example

```yml
jobs:
  deploy:
    runs-on: ubuntu-20.04
    environment: ${{ github.event.inputs.environment }}
    steps:
      - uses: actions/checkout@v2
      
      - name: AZ Login
        uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - uses: deploy-release-for-demoapp14.sh

      - name: Annotate the release
        uses: im-open/create-app-insights-annotation@v1.0.0
        with:
          subscriptionId: ${{ secrets.ARM_SUBSCRIPTION_ID }}
          resourceGroupName: ${{ env.RESOURCE_GROUP }}
          appInsightsResourceName: 'demoapp14-APPINSIGHTS'
          releaseName: ${{ github.run_id }}.${{ github.run_number }}
          category: 'Deployment'
          customMetaData: 'ProjectName=demoapp14,DeployedBy=DA'
```


## Code of Conduct

This project has adopted the [im-open's Code of Conduct](https://github.com/im-open/.github/blob/master/CODE_OF_CONDUCT.md).

## License

Copyright &copy; 2021, Extend Health, LLC. Code released under the [MIT license](LICENSE).

[Create release annotations with Azure CLI]: https://docs.microsoft.com/en-us/azure/azure-monitor/app/annotations#create-release-annotations-with-azure-cli
[viewing the annotations]: https://docs.microsoft.com/en-us/azure/azure-monitor/app/annotations#view-annotations