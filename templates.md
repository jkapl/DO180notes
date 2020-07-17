# OpenShift Templates

## Intro

Templates allow a developer to combine a set of OpenShift resources in a single YAML or JSON file, and thus to encapsulate an application along with any configurable parameters unique to a particular deployment. From the OpenShift documentation:

> A template describes a set of objects that can be parameterized and processed to produce a list of objects for creation by OpenShift Container Platform. A template can be processed to create anything you have permission to create within a project, for example services, build configurations, and DeploymentConfigs. A template may also define a set of labels to apply to every object defined in the template.

OpenShift includes many default templates. A common way to share templates among teams is to store them in a shared project and give developers view access to the shared project.

## Using the CLI
The two main commands for working with templates are `oc new-app` and `oc process`. `oc new-app` actually creates resources from the template, whereas `oc process` creates a resource list, which then needs to be piped to a file or passed to the `oc create` command:
```
oc process -f my-template.yaml -p MY_PARAM=foo | oc create -f -
```
With `oc new-app`:
```
oc new-app -f my-template.yaml -p MY_PARAM=foo
```
Red Hat recommends using the `oc new-app` command to ultimately create the application resources.

Below are some additional useful commands for interacting with templates. 

| Command | Output |
|-----|---------|
|oc process --parameters -f <filename> | List all parameters in the specified template file|
|oc process --parameters <template_name> | List all parameters in the template |
|oc create -f <filename> | Add new template to OpenShift |
|oc describe template <name-of-template> | Provide a detailed description of a template including any parameters |
|oc get -o yaml template <name-of-template> > file.yaml | Output the replica of a template resource in YAML or JSON format |
|oc new-app -f <filename> | Deploy an application based on the template file |
|oc get -o yaml svc,route,is,bc,secret,pvc,dc > draft-template.yaml | Get resources from current state of application to store in a draft template file - need to make some edits to adapt to template format |

## Example Templates

```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: my-nodejs-app
  annotations:
    description: "My Hello World App"
objects:
- apiVersion: v1
  kind: Pod
  metadata:
    name: hello-world
  spec:
    containers:
      - env:
        - name: MESSAGE
          value: ${MY_MESSAGE}
        image: quay.io/nodejs
        name: node-app
        ports:
        - containerPort: 8080
          protocol: TCP
parameters:
- description: Message to display
  name: MY_MESSAGE
  required: true
  value: This is the default message
```

### Annotations

There are many possible annotations to provide in the `metadata.annotations` field. Some of these like `iconClass` and `tags` serve a specific purpose in OpenShift. For instance, a logo image can be associated with the template via `iconClass`.

### Parameters

Developers can specify default values for parameters and can make them required, or not. They can also be generated using a regular expression.

## Multicontainer Templates

One way to create a multicontainer template is to redirect the output of the `oc new-app` command to a file, and then to combine application resources into single template file. The output of `oc new-app --name=mynodeapp --image-stream=nodejs -o yaml` is a Kubernetes resource called a `List`, which is a collection of items.

Another way is to take an application as it's currently deployed in OpenShift and output the YAML description of those resources to files. `oc get all` won't actually output the resources necessary for a template, so instead it's best to specify the resources necesary directly, and in the order in which they need to be deployed:
```
oc get -o yaml svc,route,is,bc,secret,pvc,dc > draft-template.yaml
```
<b>Note:</b> Resources are created in the order in which they appear in the template file. There is not a `runAfter` or `from` field as with Tekton or other cloud native automation tools.

The above `oc get` command will output a `List` resource. To convert this to a `Template` resource, replace the `Kind` field with `Template`, provide a `metadata.name` field, and replace `items` with `objects`. Then remove any extraneous fields such as assigned IPs or status values.

## Resources
https://docs.openshift.com/container-platform/4.4/openshift_images/using-templates.html