# Отчет по практической работе №2
## Студент: Уханов Алексей Константинович
## Группа: БСБО-16-23
## Дата выполнения: 24.04.2026
### 1. Информация о кластере
#### 1.1 Статус Minikube
\`\`\`
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
\`\`\`
#### 1.2 Узлы кластера
\`\`\`
NAME       STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION    CONTAINER-RUNTIME

minikube   Ready    control-plane   12h   v1.35.1   192.168.49.2   <none>        Debian GNU/Linux 12 (bookworm)   6.4.16-linuxkit   docker://29.2.1
\`\`\`
### 2. Созданные ресурсы
#### 2.1 Pods
\`\`\`
NAME                                  READY   STATUS    RESTARTS   AGE     IP            NODE       NOMINATED NODE   READINESS GATES

go-app-deployment-7868cc585f-jd44s    1/1     Running   0          20m     10.244.0.33   minikube   <none>           <none>

go-app-deployment-7868cc585f-wrjmk    1/1     Running   0          20m     10.244.0.34   minikube   <none>           <none>

nginx-deployment-5997d9c5cf-h2tkw     1/1     Running   0          3m51s   10.244.0.43   minikube   <none>           <none>

nginx-deployment-5997d9c5cf-nvt9c     1/1     Running   0          3m52s   10.244.0.42   minikube   <none>           <none>

postgres-deployment-75fc8ccd7-gh6sh   1/1     Running   0          20m     10.244.0.37   minikube   <none>           <none>
\`\`\`
#### 2.2 Deployments
\`\`\`
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE

go-app-deployment     2/2     2            2           20m

nginx-deployment      2/2     2            2           20m

postgres-deployment   1/1     1            1           20m
\`\`\`
#### 2.3 Services
\`\`\`
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE

go-app-service     ClusterIP   10.99.138.122   <none>        8080/TCP       21m

kubernetes         ClusterIP   10.96.0.1       <none>        443/TCP        12h

nginx-service      NodePort    10.107.2.68     <none>        80:30080/TCP   21m

postgres-service   ClusterIP   10.106.116.36   <none>        5432/TCP       21m
\`\`\`
### 3. Скриншоты работы приложения
#### 3.1 Главная страница
![Главная страница](screenshots/main-page.png)
#### 3.2 Дашборд Kubernetes
![Kubernetes Dashboard](screenshots/dashboard.png)
#### 3.3 Результат GET /api/users
![API Response](screenshots/api-response.png)
### 4. Эксперименты с масштабированием
#### 4.1 Масштабирование до 5 реплик
``` kubectl scale deployment nginx-deployment --replicas=5 ```

NAME                                READY   STATUS    RESTARTS   AGE

nginx-deployment-5997d9c5cf-4vwl8   1/1     Running   0          4s

nginx-deployment-5997d9c5cf-f4s4b   1/1     Running   0          4s

nginx-deployment-5997d9c5cf-h2tkw   1/1     Running   0          13m

nginx-deployment-5997d9c5cf-nvt9c   1/1     Running   0          13m

nginx-deployment-5997d9c5cf-qlll6   1/1     Running   0          4s
#### 4.2 Проверка распределения нагрузки
```
for i in {1..20}; do curl -s http://127.0.0.1:60918/api/health; echo ""; sleep 0.2; done
```
```
kubectl logs -l app=nginx --prefix --tail=2
```
[pod/nginx-deployment-5997d9c5cf-4vwl8/nginx] 10.244.0.1 - - [25/Apr/2026:08:37:26 +0000] "GET /api/health HTTP/1.1" 200 21 "-" "curl/8.7.1"

[pod/nginx-deployment-5997d9c5cf-4vwl8/nginx] 10.244.0.1 - - [25/Apr/2026:08:37:27 +0000] "GET /api/health HTTP/1.1" 200 21 "-" "curl/8.7.1"

[pod/nginx-deployment-5997d9c5cf-f4s4b/nginx] 10.244.0.1 - - [25/Apr/2026:08:37:29 +0000] "GET /api/health HTTP/1.1" 200 21 "-" "curl/8.7.1"

[pod/nginx-deployment-5997d9c5cf-f4s4b/nginx] 10.244.0.1 - - [25/Apr/2026:08:37:29 +0000] "GET /api/health HTTP/1.1" 200 21 "-" "curl/8.7.1"

[pod/nginx-deployment-5997d9c5cf-h2tkw/nginx] 10.244.0.1 - - [25/Apr/2026:08:37:30 +0000] "GET /api/health HTTP/1.1" 200 21 "-" "curl/8.7.1"

[pod/nginx-deployment-5997d9c5cf-h2tkw/nginx] 10.244.0.1 - - [25/Apr/2026:08:37:31 +0000] "GET /api/health HTTP/1.1" 200 21 "-" "curl/8.7.1"

[pod/nginx-deployment-5997d9c5cf-nvt9c/nginx] 10.244.0.1 - - [25/Apr/2026:08:37:30 +0000] "GET /api/health HTTP/1.1" 200 21 "-" "curl/8.7.1"

[pod/nginx-deployment-5997d9c5cf-nvt9c/nginx] 10.244.0.1 - - [25/Apr/2026:08:37:31 +0000] "GET /api/health HTTP/1.1" 200 21 "-" "curl/8.7.1"

[pod/nginx-deployment-5997d9c5cf-qlll6/nginx] 10.244.0.1 - - [25/Apr/2026:08:37:29 +0000] "GET /api/health HTTP/1.1" 200 21 "-" "curl/8.7.1"

[pod/nginx-deployment-5997d9c5cf-qlll6/nginx] 10.244.0.1 - - [25/Apr/2026:08:37:30 +0000] "GET /api/health HTTP/1.1" 200 21 "-" "curl/8.7.1"

### 5. GitHub Actions
#### 5.1 Успешная валидация манифестов
![GitHub Actions Validation](screenshots/github-actions.png)
### 6. Ответы на контрольные вопросы
1. В чем разница между Pod и Deployment?
2. Для чего нужен Service типа ClusterIP?
3. Как ReplicaSet обеспечивает самовосстановление?
4. Что произойдет с приложением, если удалить под PostgreSQL?
### 7. Выводы
[Опишите, что нового узнали, с какими трудностями столкнулись]