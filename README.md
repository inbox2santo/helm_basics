# helm_basics

Here's a basic Helm chart structure for your Python application. This chart assumes that your Docker image is hosted on Artifactory and that your application can be deployed as a Kubernetes Deployment with a Service to expose it.

1. Directory Structure
perl
Copy code
my-python-app/
├── charts/
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── _helpers.tpl
├── Chart.yaml
├── values.yaml
2. Chart.yaml
This file defines your Helm chart.

yaml
Copy code
apiVersion: v2
name: my-python-app
description: A Helm chart for deploying a Python application
version: 0.1.0
appVersion: "1.0.0"
3. values.yaml
This file allows you to configure your chart. You can override these values when installing the chart.

yaml
Copy code
replicaCount: 1

image:
  repository: <artifactory-repo-url>/<image-name>
  pullPolicy: IfNotPresent
  tag: "latest"

service:
  type: ClusterIP
  port: 80

resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi

nodeSelector: {}
tolerations: []
affinity: {}
4. templates/deployment.yaml
This file defines the Kubernetes Deployment for your application.

yaml
Copy code
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "my-python-app.fullname" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ include "my-python-app.name" . }}
  template:
    metadata:
      labels:
        app: {{ include "my-python-app.name" . }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: 80
          resources:
            limits:
              cpu: {{ .Values.resources.limits.cpu }}
              memory: {{ .Values.resources.limits.memory }}
            requests:
              cpu: {{ .Values.resources.requests.cpu }}
              memory: {{ .Values.resources.requests.memory }}
      nodeSelector: {{ .Values.nodeSelector }}
      tolerations: {{ .Values.tolerations }}
      affinity: {{ .Values.affinity }}
5. templates/service.yaml
This file defines the Kubernetes Service for your application.

yaml
Copy code
apiVersion: v1
kind: Service
metadata:
  name: {{ include "my-python-app.fullname" . }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: 80
  selector:
    app: {{ include "my-python-app.name" . }}
6. templates/_helpers.tpl
This file contains helper templates for generating names.

yaml
Copy code
{{- define "my-python-app.name" -}}
{{- .Chart.Name | trunc 63 | trimSuffix "-" -}}
{{- end -}}

{{- define "my-python-app.fullname" -}}
{{- .Release.Name | trunc 63 | trimSuffix "-" -}}
{{- end -}}
How to Use This Helm Chart
Replace <artifactory-repo-url>/<image-name> in values.yaml with the actual URL of your Docker image in Artifactory.
Customize the values.yaml file as needed.
Package and deploy the chart:
bash
Copy code
helm install my-python-app ./my-python-app
This should give you a good starting point for deploying your Python application using Helm.








