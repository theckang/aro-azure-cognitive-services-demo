# Overview

Azure Red Hat OpenShift (ARO) demo using Azure Cognitive Services APIs.

## Overview



## Prerequisites

* Azure Red Hat OpenShift 4 Cluster
* Admin access to OpenShift
* [OpenShift CLI](https://docs.openshift.com/container-platform/4.6/cli_reference/openshift_cli/getting-started-cli.html)

## Setup

Follow these [instructions](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#prerequisites) to create a Cognitive Service in your Azure subscription.

Set your key in the terminal

```bash
export COGNITIVE_SERVICE_SUBSCRIPTION_KEY=<enter-your-key>
```

## Translate, Synthesize, and Text-to-Speech

We are going to build a Flask app based on this [Microsoft Tutorial](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

Deploy the app

```bash
oc new-app python:3.6-ubi8~https://github.com/theckang/Text-Translation-API-V3-Flask-App-Tutorial --name cognitive-service-demo --env COGNITIVE_SERVICE_SUBSCRIPTION_KEY=$COGNITIVE_SERVICE_SUBSCRIPTION_KEY
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

https://docs.microsoft.com/en-us/azure/cognitive-services/containers/azure-kubernetes-recipe

