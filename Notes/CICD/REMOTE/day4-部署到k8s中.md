接上文 [[day3-继续搭建简单流水线]]

## 2. 部署到 k8s 中

K8s 是干嘛的我就不多赘述了，在项目中添加一个 deployment.yaml 用于 k8s 部署描述

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-first-api-deployment
  labels:
    app: my-first-api
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-first-api
  template:
    metadata:
      labels:
        app: my-first-api
    spec:
      containers:
      - name: my-first-api
        image: IMAGE_PLACEHOLDER
        imagePullPolicy: Always
        ports:
        - containerPort: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: my-first-api-service
spec:
  selector:
    app: my-first-api
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: ClusterIP
```

运行命令使 k8s 信任仓库

```bash
sudo tee /etc/rancher/k3s/registries.yaml <<EOF
mirrors:
  "172.16.62.47:5000":
    endpoint:
      - "http://172.16.62.47:5000"
EOF
sudo systemctl restart k3s
```

更改 jenkinsfile

```groovy
pipeline {
    agent any
    environment {
        IMAGE_NAME = "172.16.62.47:5000/my-first-api:build-${BUILD_NUMBER}"
        K8S_DEPLOY_FILE = "deployment.generated.yaml"
    }
    stages {
        stage('构建 Docker 镜像') {
            steps {
                script {
                    echo "正在构建镜像: ${IMAGE_NAME}"
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }
        stage('推送镜像') {
            steps {
                script {
                    echo "正在推送镜像到私有仓库..."
                    sh "docker push ${IMAGE_NAME}"
                }
            }
        }
        stage('部署到 K3s') {
            steps {
                script {
                    echo "deploying..."
                    sh """
                        sed 's|IMAGE_PLACEHOLDER|${IMAGE_NAME}|g' deployment.yaml > ${K8S_DEPLOY_FILE}
                    """
                    sh "kubectl apply -f ${K8S_DEPLOY_FILE}"
                    sh "kubectl rollout status deployment/my-first-api-deployment --timeout=60s"
                    echo "depolyment ok"
                }
            }
        }
    }
    post {
        success {
            echo 'build successed'
        }
        failure {
            echo 'build failed'
        }
    }
}
```

尝试推送，build 成功

![[Pasted image 20251210175437.png]]

