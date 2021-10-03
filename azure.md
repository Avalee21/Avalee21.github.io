This article will show you how to create an machine learning pipeline via Azure Python SDK.

You'll need an Azure subscription as a precondition.

**Workspace**

[how to manage workspace](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-manage-workspace?tabs=python)

```
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
           subscription_id='<azure-subscription-id>',
           resource_group='myresourcegroup',
           create_resource_group=True,
           location='eastus2'
           )
```
`ws.write_config(path="./file-path", file_name="ws_config.json")`
