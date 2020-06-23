# OpenShift Templates

## Intro

Templates allow a developer to combine a set of OpenShift resources in a single YAML or JSON file, and thus to encapsulate an application along with any configurable parameters unique to a particular deployment. From the OpenShift documentation:

> A template describes a set of objects that can be parameterized and processed to produce a list of objects for creation by OpenShift Container Platform. A template can be processed to create anything you have permission to create within a project, for example services, build configurations, and DeploymentConfigs. A template may also define a set of labels to apply to every object defined in the template.

## Using the CLI

Some useful commands for interacting with templates are below:

| Command | Output |
|-----|---------|
|oc process --parameters -f <filename> | List all parameters in the specified template file|
|oc process --parameters -n <project>  <template_name> | List all parameters in the template stored in the specified project |




## Resources
https://docs.openshift.com/container-platform/4.4/openshift_images/using-templates.html