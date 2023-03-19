# KUBE

### ขั้นตอนการทำงาน

- 1.การติดตั้ง kubectl กับ minikube
- 2.การติดตั้ง docker engine
- 3.การ deploy Traefik
- 4.การ deploy rancher/hello-world

##  การติดตั้ง kubectl กับ minikube

### ติดตั้ง kubectl

- ทำการโหลด kubectl จาก : https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/ หรือใช้คำสั่ง

```
curl.exe -LO "https://dl.k8s.io/release/v1.26.0/bin/windows/amd64/kubectl.exe"
```

![image](https://user-images.githubusercontent.com/115439255/226182288-9d329324-c461-4839-85d1-0f6b2b0e51db.png)

- เข้าไปสร้าง ที่ไดฟ์ C ชื่อ : kubectl แล้วนำไฟล์ที่โหลดมาเข้าไปไว้ที่โฟลเดอร์ที่เราสร้างไว้และนำ Path ไปเพิ่มใน env

![image](https://user-images.githubusercontent.com/115439255/226182622-213c9a97-e0eb-4213-9077-c3faad8ff717.png)

### ติดตั้ง minikube

- ทำการโหลด minikube จาก : https://minikube.sigs.k8s.io/docs/start/ หรือ ใช้คำสั่ง

```
  New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
  Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -   UseBasicParsing
```

![image](https://user-images.githubusercontent.com/115439255/226182910-0ef2528f-031f-4350-af89-ce09ab8fd38e.png)

- ทำการติดตั้ง minikube จากนั้นตัว minikube จะสร้าง path ใน env ให้ หรือ ใช้คำสั่ง ดังนี้เพื่อ set path

```
$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
if ($oldPath.Split(';') -inotcontains 'C:\minikube'){ `
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine) `
}
```

- ทดสอบ minikube ใช้งานได้หรือไม่ โดยการพิมพ์คำสั่ง

```
    minikube
```

## การติดตั้ง docker engine

Ref : https://docs.docker.com/desktop/install/windows-install/

กดที่ Docker Desktop for Windows

![image](https://user-images.githubusercontent.com/115439255/226183934-e1b3c6e3-50f9-48dc-97b7-3b9341295e06.png)

- ติดตั้งเสร็จแล้วลอง run minikube โดยใช้คำสั่ง
```
minikube start --driver=docker
```

- ผลลัพธ์

![image](https://user-images.githubusercontent.com/115439255/226184268-fda2b321-ca93-4d82-9146-37ff61a68a82.png)

## การ deploy Traefik
Ref : https://github.com/iamapinan/kubeplay-traefik

- ทำการเพิ่ม 127.0.0.1 traefik.spcn15.local ในไฟล์ hosts ที่ path C:\Windows\System32\drivers\etc

![image](https://user-images.githubusercontent.com/115439255/226184683-cb914850-1400-4fa6-bce0-33d6973463e4.png)

- Install Traefik โดยใช้คำสั่ง

```
kubectl apply -f https://raw.githubusercontent.com/traefik/traefik/v2.9/docs/content/reference/dynamic-configuration/kubernetes-crd-definition-v1.yml
```

- Install RBAC for Traefik โดยใช้คำสั่ง
```
kubectl apply -f https://raw.githubusercontent.com/traefik/traefik/v2.9/docs/content/reference/dynamic-configuration/kubernetes-crd-rbac.yml
```

- สร้าง namespace โดยใช้คำสั่ง
```
kubectl create namespace spcn15
```

- Install Helmchart โดยใช้คำสั่ง
```
helm repo add traefik https://traefik.github.io/charts
helm repo update
helm install traefik traefik/traefik
```

- สร้างไฟล์ auth-secret โดยใช้คำสั่ง (ใน git bash)
```
htpasswd -nB user | tee auth-secret                  /// ตรง user สามารถเปลี่ยนได้

```
```
kubectl create secret generic -n " กำหนดชื่อ " dashboard-auth-secret \ --from-file=users=auth-secret -o yaml --dry-run=client | tee dashboard-secret.yaml
```
- เมื่อ run เสร็จแล้วจะได้ไฟล์ dashboard-secret.yaml มา และนำข้อมูลตรง user ไปใส่ในไฟล์ traefik-dashboard.yaml ให้ตรงกัน

![image](https://user-images.githubusercontent.com/115439255/226185679-d1b8412f-b660-4ddd-ad3a-cca8da918397.png)
![image](https://user-images.githubusercontent.com/115439255/226185729-0cfe5332-c8c6-412c-a915-e590f9df2bb0.png)

- Deploy โดยใช้คำสั่ง
```
kubectl apply -f traefik-dashboard.yaml
```

- ทดสอบ deploy traefik โดย domain ที่เราตั้งไว้ คือ https://traefik.spcn15.local/dashboard/#/

![image](https://user-images.githubusercontent.com/115439255/226185931-ce817b96-c000-4b54-94b1-6b4adf95486d.png)


