
# Continuous Integration (Windows) 

---

### **1. Úvod**  

Continuous Integration (CI) s Dockerem na Windows umoòuje automatizovat sestavování, testování a nasazování aplikací pomocí kontejnerù. Klíèové vıhody:  
- **Konzistence**: Kadı build bìí v izolovaném prostøedí.  
- **Reprodukovatelnost**: Eliminace problémù typu *"funguje to na mém poèítaèi"*.  
- **Škálovatelnost**: Paralelní bìh testù a nasazování.  
Nástroje: GitHub Actions, Azure DevOps, Jenkins, GitLab CI.

---

### **2. Klíèové komponenty**  

#### **a) Docker Image jako buildovací prostøedí**  

- **Vyuití**: Spouštìní testù a skriptù v pøeddefinovaném kontejneru.  
- **Pøíklad Dockerfile pro CI**:  
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

- **Fáze**:  
  1. **Build**: Sestavení Docker image z kódu.  
  2. **Test**: Spuštìní unit/integrovanıch testù v kontejneru.  
  3. **Push**: Nahrání image do registru (Docker Hub, Azure Container Registry).  
  4. **Deploy**: Nasazení do stagingu/produkce.  

#### **c) Multi-stage Builds**  

- **Cíl**: Redukce velikosti finálního image odstranìním vıvojovıch nástrojù.  
- **Windows specifické problémy**: Velikost základních obrazù (napø. `windows/servercore` ~3 GB).  

---

### **3. Nastavení CI na Windows**  

#### **a) Pøíprava Dockerfile**  

- Pouívejte oficiální Windows základní obrazy (napø. `mcr.microsoft.com/windows/servercore:ltsc2022`).  
- Minimalizujte poèet vrstev (kombinujte pøíkazy `RUN`).  

#### **b) Konfigurace CI Pipeline (Azure DevOps)**  

Pøíklad `azure-pipelines.yml`:  
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

#### **c) Cache pro zrychlení buildù**  

- **Vyuití vrstvení Dockeru**:  
  ```yaml
  # GitHub Actions – cache Docker layers
  - name: Cache Docker layers
    uses: actions/cache@v3
    with:
      path: /tmp/.buildx-cache
      key: ${{ runner.os }}-buildx-${{ github.sha }}
      restore-keys: ${{ runner.os }}-buildx-
  ```  

#### **d) Testování v kontejneru**  

Spuštìní testù pøímo v kontejneru:  
```powershell
docker build -t moje-aplikace-test .
docker run moje-aplikace-test dotnet test
```  

---

### **4. Pøíklady CI Pipeline**  

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
  - Pouívejte `mcr.microsoft.com/windows/nanoserver` pro minimální základ.  
  - Odstraòte doèasné soubory v jednom `RUN` pøíkazu.  
- **Bezpeènost**:  
  - Skenujte obrazy nástroji jako Trivy nebo Azure Security Center.  
  - Nepouívejte `latest` tag pro produkci.  
- **Monitorování CI/CD**:  
  - Mìøení èasu buildù a detekce flaky testù.  

---

### **6. Windows-specifické vızvy**  

- **Pomalé buildy**: Kvùli velikosti Windows obrazù.  
  - Øešení: Pouívejte cached vrstvy a SSD disky.  
- **Omezení WSL2**: Pro Linux kontejnery na Windows.  
  - Nastavte dostatek pamìti v `.wslconfig`.  
- **Firewall a síování**:  
  - Povolte komunikaci mezi kontejnery a CI servery.  

---

### **7. Nástroje a integrace**  

- **Azure Container Registry (ACR)**: Ukládání a správa Windows images.  
- **Jenkins s Windows agenty**: Pro komplexní CI/CD workflows.  
- **Testcontainers**: Automatizace testù s doèasnımi kontejnery (podpora .NET).  

---

### **8. Øešení problémù**  

- **Chyby pøi sestavování**:  
  - `No space left on device`: Zvìtšete disk CI agenta nebo vyèistìte cache.  
  - `Windows container failed to start`: Ovìøte kompatibilitu hostitelského OS a image.  
- **Problémy se síováním**:  
  - Pouijte `--network=host` pro Linux kontejnery na WSL2.  

---

### **9. Shrnutí** 

- **CI s Dockerem na Windows** vyaduje optimalizaci obrazù a správu zdrojù.  
- **Klíèové kroky**: Automatizace buildù, testování v kontejnerech, integrace s registry.  
- **Nástroje**: GitHub Actions, Azure DevOps, Docker Compose.  
- **Windows specifika**: Velikost obrazù, Hyper-V izolace, síová konfigurace.  

**Doporuèení**:  
- Pro produkci pouívejte **multi-stage builds** a **Azure ACR**.  
- Pravidelnì aktualizujte základní obrazy kvùli bezpeènostním záplatám.  
- Monitorujte vıkon CI pipeline a odstraòujte úzká místa.  

--- 

**Viz také**:  
- [Docker CI/CD Best Practices](https://docs.docker.com/ci-cd/best-practices/)  
- [Azure DevOps for Windows Containers](https://learn.microsoft.com/en-us/azure/devops/pipelines/ecosystems/containers/build-containers?view=azure-devops)
