# 1. Обязательная часть задания.

1. Запустить minikube с активированным metrics-server

    ```
    minikube start --addons=metrics-server
    ```

2. Проверить доступность metrics-server

    ```
    kubectl get deployment metrics-server -n kube-system
    ```

3. Развернуть приложение scaletestapp в кластере Kubernetes в Minikube

    ```
    kubectl apply -f scaletestapp-deployment.yaml
    kubectl apply -f scaletestapp-service.yaml
    ```

4. Применить манифест для Horizontal Pod Autoscaler и проверить его состояние
    
    ```
    kubectl apply -f hpa.yaml
    kubectl get hpa
    ```

5. Открыть доступ к приложению вне Minikube для тестирования с Locust

    ```
    minikube service scaletestapp-service --url
    ```

6. Запустить Locust, настроить параметры нагрузочного тестирования в Web UI и запустить.

    ```
    locust
    ```

7. Открыть дашборд Minikube, провести наблюдение, сделать скриншоты

    ```
    minikube dashboard
    ```
    См. скриншоты: Dashboard_ClusterEvents.png, Dashboard_Workloads.png, Dashboard_WorkloadsPods.png.

8. Собрать логи 

    ```
    minikube kubectl events > cluster_events.log
    ```
    См. события в cluster_events.log:
    ```
    68s                     Normal    ScalingReplicaSet                   Deployment/scaletestapp-deployment             Scaled up replica set scaletestapp-deployment-8dc9df94d to 2 from 1
    68s                     Normal    SuccessfulRescale                   HorizontalPodAutoscaler/scaletestapp-hpa       New size: 2; reason: memory resource utilization (percentage of request) above target
    ```

9. Остановить Minikube для завершения работы

    ```
    minikube stop
    ```


# 2. Дополнительная часть задания.

1. Запустить minikube с активированным metrics-server

    ```
    minikube start --addons=metrics-server
    ```

2. Проверить доступность metrics-server

    ```
    kubectl get deployment metrics-server -n kube-system
    ```

3. Установить Prometheus в кластере Kubernetes в Minikube и проверить состояние подов

    ```
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update
    helm install prometheus-operator prometheus-community/kube-prometheus-stack

    kubectl --namespace default get pods -l "release=prometheus-operator"
    ```

4. Развернуть приложение scaletestapp в кластере Kubernetes в Minikube

    ```
    kubectl apply -f scaletestapp-deployment.yaml
    kubectl apply -f scaletestapp-service.yaml
    ```

5. Сбросить параметры Prometheus Service Monitor по умолчанию

    ```
    helm upgrade prometheus-operator prometheus-community/kube-prometheus-stack --namespace default --values Additional\values_reset.yaml
    ```

6. Применить манифест Service Monitor для настройки сбора метрик scaletestapp

    ```
    kubectl apply -f Additional\ServiceMonitor.yaml
    ```

7. Открыть доступ к Prometheus вне Minikube и проверить сбор метрик scaletestapp

    ```
    kubectl -n default port-forward svc/prometheus-operated 9090:9090
    ```
    См. скриншот Additional\Prometheus_scaletestapp_metrics.png

8. Установить Prometheus Adapter с настройкой кастомных метрик

    ```
    helm install prometheus-adapter prometheus-community/prometheus-adapter -f Additional\values.yaml
    ```

9. Проверить доступность кастомных метрик

    ```
    kubectl get --raw /apis/custom.metrics.k8s.io/v1beta1
    ```

10. Применить манифест для Horizontal Pod Autoscaler и проверить его состояние

    ```
    kubectl apply -f Additional\hpa.yaml
    kubectl get hpa
    ```

11. Открыть доступ к приложению вне Minikube для тестирования с Locust

    ```
    minikube service scaletestapp-service --url
    ```

12. Запустить Locust, настроить параметры нагрузочного тестирования в Web UI и запустить

    ```
    locust
    ```

13. Открыть дашборд Minikube, провести наблюдение, сделать скриншоты

    ```
    minikube dashboard
    ```
    См скриншоты: Additional\Dashboard_ClusterEvents.png, Additional\Dashboard_ServiceServicesPods.png, Additional\Dashboard_Workloads.png.

14. Собрать логи 
    
    ```
    minikube kubectl events > cluster_events.log
    ```
    См. события в Additional\cluster_events.log 
    ```
    4m39s               Normal    ScalingReplicaSet                   Deployment/scaletestapp-deployment                                 Scaled up replica set scaletestapp-deployment-8dc9df94d to 2 from 1
    4m39s               Normal    SuccessfulRescale                   HorizontalPodAutoscaler/scaletestapp-hpa                           New size: 2; reason: pods metric http_requests_per_second above target
    ```
   
15. Остановить Minikube для завершения работы

    ```
    minikube stop
    ```

