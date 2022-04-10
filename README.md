# Datree Helm Plugin

A Helm plugin to validate charts against the Datree policy  

## Installation
```
helm plugin install https://github.com/datreeio/helm-datree
```
⚠️ Helm plugins are not supported on Windows OS ⚠️  
_Windows users can work around this by using [Helm under WSL](https://github.com/helm/helm-2to3#for-windows-using-wsl)_
</br>

### Update Datree's plugin version
```
helm plugin update datree
```
### Uninstall
```
helm plugin uninstall datree
```

## Usage

### Trigger datree policy check via the helm CLI
```
helm datree test [CHART_DIRECTORY]
```

### Passing arguments
If you need to pass helm arguments to your template, you will need to add `--` before them:
```
helm datree test [CHART_DIRECTORY] -- --values values.yaml --set name=prod
```

### Check plugin version
```
helm datree version
```

### See help text
```
helm datree help
```

### Using other helm command
Helm might be installed through other tooling like microk8s. The `DATREE_HELM_COMMAND` allows specifying a command to run helm (default: `helm`):
```
DATREE_HELM_COMMAND="microk8s helm3" helm datree test [CHART_DIRECTORY]
```

## Example

```
helm plugin install https://github.com/datreeio/helm-datree
git clone git@github.com:datreeio/examples.git
helm datree test examples/helm-chart/nginx
```

![image](https://user-images.githubusercontent.com/19731161/131975552-b66a84f8-5aa9-4d70-a08e-aae97aa76116.png)


## Troubleshooting

### Error: plugin "datree" exited with error
This is actually expected behavior because it's raised by Helm itself every time a plugin returns a [non-zero exit code](https://www.gnu.org/software/bash/manual/html_node/Exit-Status.html#:~:text=A%20non%2Dzero%20exit%20status,N%20as%20the%20exit%20status.).  
Therefore, if you will run datree plugin on a Chart that will pass the policy check, it will return 0 as exit code, and you will not see this error.

### K8s schema validation error
This error occurs when trying to scan Chart.yaml or values.yaml files instead of the chart directory.  
**Solution:** Pass the helm chart directory path to Datree's CLI, instead of to the file itself:  
* Correct - `helm datree test examples/helm-chart/nginx`
* Wrong - `helm datree test examples/helm-chart/nginx/values.yaml`

### The policy check returns false-positive results
The best way to determine if a false-positive result is a bug or a true misconfiguration, is by rendering the Kubernetes manifest with helm and then checking it manually:
```
helm template [CHART_DIRECTORY]
```
If after eyeballing the rendered manifest you still suspect it's a bug, please open an issue. 
