This article will show you how to create an machine learning pipeline via Azure Python SDK.

 
```
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig, Dataset, Datastore
from azureml.core.runconfig import RunConfiguration
from azureml.data.datapath import DataPath, DataPathComputeBinding
from azureml.pipeline.core import Pipeline, PipelineParameter
from azureml.pipeline.steps import PythonScriptStep
from azureml.core.compute import ComputeTarget

ws = Workspace.from_config('my_workspace_config.json')
compute_target = ComputeTarget(workspace=ws, name=my_cluster_name)
env = Environment.get(workspace=ws, name="my_environment")
src = RunConfiguration()
src.target = compute_target
src.environment = env
datastore = Datastore(ws, "my_datastore")

train_input_data_path = DataPath(datastore = datastore, path_on_datastore=f'{train_input_folder}/preprocess_output/{mode}_data/train/processed_trainset_{mode}.pkl')
datapath_parameter = PipelineParameter(name="train_input_data_path", default_value=train_input_data_path)
train_input_data_path = (datapath_parameter, DataPathComputeBinding(mode='mount'))

test_input_data_path = DataPath(datastore = datastore, path_on_datastore=f'{test_input_folder}/preprocess_output/{mode}_data/test/processed_testset_{mode}.pkl')
datapath_parameter = PipelineParameter(name="test_input_data_path", default_value=test_input_data_path)
test_input_data_path = (datapath_parameter, DataPathComputeBinding(mode='mount'))

step = PythonScriptStep(
             source_directory= my_project_path,
             script_name='my_project.py', 
             arguments=[
             "--train_data_path", train_input_data_path,
             "--test_data_path", test_input_data_path,
             "--model_path", model_path,
             "--log_path", log_path,
             ],
             inputs = [train_input_data_path, test_input_data_path],
             compute_target=compute_target,
             runconfig=src,
             allow_reuse=False
)

my_pipeline = Pipeline(workspace=ws, steps=[step])
experiment = Experiment(workspace=ws, name=my_experiment_name)
run = experiment.submit(my_pipeline)
run.set_tags({"mode": mode})
print(run.get_portal_url())
run.wait_for_completion(show_output=True)
```
