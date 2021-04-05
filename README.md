# Overview

Azure Red Hat OpenShift (ARO) demo using Azure Cognitive Services APIs.

## Overview



## Prerequisites

* Azure Red Hat OpenShift 4 Cluster
* Admin access to OpenShift
* [OpenShift CLI](https://docs.openshift.com/container-platform/4.6/cli_reference/openshift_cli/getting-started-cli.html)

## Setup

Follow these [instructions](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#prerequisites) to create a Cognitive Service in your Azure subscription.

**Important**: For the purposes of this demo, select the `West US` region for your Cognitive Service.

For the Language Detection Container demo, you must also submit a request to approve your Azure subscription for access to Cognitive Service containers.  See [here](https://docs.microsoft.com/en-us/azure/cognitive-services/containers/container-faq#how-do-i-access-and-use-a-gated-preview-container).

Set your key in the terminal

```bash
export COGNITIVE_SERVICE_SUBSCRIPTION_KEY=<enter-your-key>
```

## Translate, Synthesize, and Text-to-Speech

We are going to build and deploy a Flask app based on this [Microsoft Tutorial](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

Build and deploy the app

```bash
oc new-app python:3.6-ubi8~https://github.com/theckang/Text-Translation-API-V3-Flask-App-Tutorial \
  --name cognitive-service-demo \
  --env COGNITIVE_SERVICE_SUBSCRIPTION_KEY=$COGNITIVE_SERVICE_SUBSCRIPTION_KEY
```

Expose the app

```bash
oc expose svc cognitive-service-demo
```

Open the app in your browser

```bash
echo $(oc get route cognitive-service-demo --template='http://{{.spec.host}}')
```

## Text Analytics using Language Detection Container

We are going to build and deploy a Language Detection application based on this [Microsoft Quickstart](https://docs.microsoft.com/en-us/azure/cognitive-services/containers/azure-kubernetes-recipe).  One container is the application frontend website.  The other container is a Cognitive Service container that will detect the language of the text passed to the website.  See the [quickstart documentation](https://docs.microsoft.com/en-us/azure/cognitive-services/containers/azure-kubernetes-recipe#the-sample-containers) for more information.

Clone my fork

```bash
git clone https://github.com/theckang/cognitive-services-containers-samples
```

Build the frontend website

```bash
oc new-build https://github.com/theckang/cognitive-services-containers-samples --context-dir dotnet/Language/FrontendService --strategy docker
```

Import Azure Cognitive Service Language images to the internal ARO registry

```bash
oc import-image cognitive-services-language --confirm --all --from mcr.microsoft.com/azure-cognitive-services/textanalytics/language
```

Allow resources to reference the image streams

```bash
oc set image-lookup cognitive-services-containers-samples
oc set image-lookup cognitive-services-language
```

Open the `cognitive-services-containers-samples/Kubernetes/language/language.yml` file in your favorite editor and insert your subscription key for the `apikey` field.

> You can view your subscription key with the command below

```bash
    echo $COGNITIVE_SERVICE_SUBSCRIPTION_KEY
```

Deploy the containers

```bash
oc create -f cognitive-services-containers-samples/Kubernetes/language/language.yml
```
