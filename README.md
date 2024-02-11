#005: Kubernetes monitoring
Una empresa ha recibido quejas de sus usuarios sobre el rendimiento lento de su sitio web/aplicación en determinados momentos. El CISO ha solicitado al equipo DevOps que monitorice la web/aplicación para identificar el cuello de botella que causa la lentitud.
Objetivos:
```
- Desplegar 3 o 4 pods utilizando la imagen https://github.com/cloudogu/hello-k8s, de forma similar a como se hizo en el taller 3.
- Implementar un sistema de monitorización con Prometheus y Grafana para el cluster de minikube, utilizando Helm para su instalación.
- Identificar las métricas más relevantes para detectar problemas de rendimiento.
- Ver si es posible configurar alertas para notificar al equipo DevOps sobre problemas de rendimiento y caídas de pods.
- Si es posible, configurar las alertas y simular la caída de algún pod.
```
 
Tips:
```
- alert-manager
- kuberntes_sd_configs
- alerting rules
```

Procedimiento:
Se crea un NS con el nombre de "monitoring"
´´´
kubectl create ns monitoring
´´´
Se despliega el helm de hello-k8 en el ns creado:
```
helm install --create-namespace --namespace monitoring custom-message ./hello-kubernetes --set message="Caso 5 del Hacktone - Monitoring"
```

Se añaden los repositorios de Grafana/Prometheus de helm:
```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

Se revisan los Values de Grafana/Prometheus, para ver los valores del chart de configuración:
```
helm show values grafana/grafana > grafana.yml
helm show values prometheus-community/prometheus > prometheus.yml
```

Se despliega el Prometheus en el mismo NS de monitorización:
```
 helm -n monitoring upgrade --install prometheus prometheus-community/prometheus  -f .\prometheus.yml
```

Despues se edita el documento YML de grafana y se despliega:
```
 helm -n monitoring upgrade --install grafana grafana/grafana  -f .\grafana.yml
```

En el mismo fichero se edita el servicio de prometheus:
```
datasources: {}
datasources.yaml:
  apiVersion: 1
  datasources:
  - name: Prometheus
    type: prometheus
    url: http://prometheus-server.monitoring.svc:80

```

Revisamos todo los SVC de Kubernetes montados en el NS monitoring:
```
kubectl get svc,pods -n monitoring

```
![image](https://github.com/robbyq92/hacktone5/assets/49034238/de7bb32b-8ae3-436e-8b95-4d46bcfa84dc)

Revisamos Grafana con el DS
![image](https://github.com/robbyq92/hacktone5/assets/49034238/ba9a93c8-2e3f-4b0c-a916-54d4494d0c0b)

Al igual que unos Dashboard que añadimos de Grafana para Prometheus:
![image](https://github.com/robbyq92/hacktone5/assets/49034238/5503c18b-915b-4bec-823f-0f48466ad932)
![image](https://github.com/robbyq92/hacktone5/assets/49034238/a7740bdd-4f3a-4d7b-909a-4383c9b292c8)


Creamos ahora uno, para sacar un Pod de los Hello-Kuberentes:
![image](https://github.com/robbyq92/hacktone5/assets/49034238/04119423-d314-4326-ad1c-78e2310be1bd)


