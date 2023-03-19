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

