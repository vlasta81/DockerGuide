
# Continuous Integration (Windows) 

---

### **1. �vod**  

Continuous Integration (CI) s Dockerem na Windows umo��uje automatizovat sestavov�n�, testov�n� a nasazov�n� aplikac� pomoc� kontejner�. Kl��ov� v�hody:  
- **Konzistence**: Ka�d� build b�� v izolovan�m prost�ed�.  
- **Reprodukovatelnost**: Eliminace probl�m� typu *"funguje to na m�m po��ta�i"*.  
- **�k�lovatelnost**: Paraleln� b�h test� a nasazov�n�.  
N�stroje: GitHub Actions, Azure DevOps, Jenkins, GitLab CI.

---

### **2. Kl��ov� komponenty**  

#### **a) Docker Image jako buildovac� prost�ed�**  

- **Vyu�it�**: Spou�t�n� test� a skript� v p�eddefinovan�m kontejneru.  
- **P��klad Dockerfile pro CI**:  
  ```dockerfile
  FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
  WORKDIR /app
  COPY . .
  RUN dotnet publish -c Release -o out

  FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS runtime
  WORKDIR /app
  COPY --from=build /app/out .
  CMD ["dotnet", "MojeAplikace.dll"]
  ```  

#### **b) CI/CD Pipeline**  

- **F�ze**:  
  1. **Build**: Sestaven� Docker image z k�du.  
  2. **Test**: Spu�t�n� unit/integrovan�ch test� v kontejneru.  
  3. **Push**: Nahr�n� image do registru (Docker Hub, Azure Container Registry).  
  4. **Deploy**: Nasazen� do stagingu/produkce.  

#### **c) Multi-stage Builds**  

- **C�l**: Redukce velikosti fin�ln�ho image odstran�n�m v�vojov�ch n�stroj�.  
- **Windows specifick� probl�my**: Velikost z�kladn�ch obraz� (nap�. `windows/servercore` ~3 GB).  

---

### **3. Nastaven� CI na Windows**  

#### **a) P��prava Dockerfile**  

- Pou��vejte ofici�ln� Windows z�kladn� obrazy (nap�. `mcr.microsoft.com/windows/servercore:ltsc2022`).  
- Minimalizujte po�et vrstev (kombinujte p��kazy `RUN`).  

#### **b) Konfigurace CI Pipeline (Azure DevOps)**  

P��klad `azure-pipelines.yml`:  
```yaml
trigger:
  - main

pool:
  vmImage: 'windows-latest'

steps:
- task: Docker@2
  inputs:
    containerRegistry: 'my-registry'
    repository: 'moje-aplikace'
    command: 'buildAndPush'
    Dockerfile: 'Dockerfile'
    tags: 'latest'
```  

#### **c) Cache pro zrychlen� build�**  

- **Vyu�it� vrstven� Dockeru**:  
  ```yaml
  # GitHub Actions � cache Docker layers
  - name: Cache Docker layers
    uses: actions/cache@v3
    with:
      path: /tmp/.buildx-cache
      key: ${{ runner.os }}-buildx-${{ github.sha }}
      restore-keys: ${{ runner.os }}-buildx-
  ```  

#### **d) Testov�n� v kontejneru**  

Spu�t�n� test� p��mo v kontejneru:  
```powershell
docker build -t moje-aplikace-test .
docker run moje-aplikace-test dotnet test
```  

---

### **4. P��klady CI Pipeline**  

#### **a) GitHub Actions pro Windows kontejner**  

```yaml
name: CI

on: [push]

jobs:
  build:
    runs-on: windows-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Build Docker image
      run: docker build -t moje-aplikace .

    - name: Run tests
      run: docker run moje-aplikace dotnet test
```  

#### **b) Docker Compose v Azure DevOps**  

```yaml
- script: |
    docker-compose -f docker-compose.ci.yml build
    docker-compose -f docker-compose.ci.yml up -d
    docker-compose run tests
  displayName: 'Build and test'
```  

---

### **5. Best Practices**  

- **Optimalizace velikosti image**:  
  - Pou��vejte `mcr.microsoft.com/windows/nanoserver` pro minim�ln� z�klad.  
  - Odstra�te do�asn� soubory v jednom `RUN` p��kazu.  
- **Bezpe�nost**:  
  - Skenujte obrazy n�stroji jako Trivy nebo Azure Security Center.  
  - Nepou��vejte `latest` tag pro produkci.  
- **Monitorov�n� CI/CD**:  
  - M��en� �asu build� a detekce flaky test�.  

---

### **6. Windows-specifick� v�zvy**  

- **Pomal� buildy**: Kv�li velikosti Windows obraz�.  
  - �e�en�: Pou��vejte cached vrstvy a SSD disky.  
- **Omezen� WSL2**: Pro Linux kontejnery na Windows.  
  - Nastavte dostatek pam�ti v `.wslconfig`.  
- **Firewall a s�ov�n�**:  
  - Povolte komunikaci mezi kontejnery a CI servery.  

---

### **7. N�stroje a integrace**  

- **Azure Container Registry (ACR)**: Ukl�d�n� a spr�va Windows images.  
- **Jenkins s Windows agenty**: Pro komplexn� CI/CD workflows.  
- **Testcontainers**: Automatizace test� s do�asn�mi kontejnery (podpora .NET).  

---

### **8. �e�en� probl�m�**  

- **Chyby p�i sestavov�n�**:  
  - `No space left on device`: Zv�t�ete disk CI agenta nebo vy�ist�te cache.  
  - `Windows container failed to start`: Ov��te kompatibilitu hostitelsk�ho OS a image.  
- **Probl�my se s�ov�n�m**:  
  - Pou�ijte `--network=host` pro Linux kontejnery na WSL2.  

---

### **9. Shrnut�** 

- **CI s Dockerem na Windows** vy�aduje optimalizaci obraz� a spr�vu zdroj�.  
- **Kl��ov� kroky**: Automatizace build�, testov�n� v kontejnerech, integrace s registry.  
- **N�stroje**: GitHub Actions, Azure DevOps, Docker Compose.  
- **Windows specifika**: Velikost obraz�, Hyper-V izolace, s�ov� konfigurace.  

**Doporu�en�**:  
- Pro produkci pou��vejte **multi-stage builds** a **Azure ACR**.  
- Pravideln� aktualizujte z�kladn� obrazy kv�li bezpe�nostn�m z�plat�m.  
- Monitorujte v�kon CI pipeline a odstra�ujte �zk� m�sta.  

--- 

**Viz tak�**:  
- [Docker CI/CD Best Practices](https://docs.docker.com/ci-cd/best-practices/)  
- [Azure DevOps for Windows Containers](https://learn.microsoft.com/en-us/azure/devops/pipelines/ecosystems/containers/build-containers?view=azure-devops)
