
# Sestavovací soubory (Windows)

---

### **1. Úvod do sestavovacích souborù** 

- **Sestavovací soubory**: Definují proces tvorby Docker image (napø. `Dockerfile`, `docker-compose.yml`, `.dockerignore`).  
- **Úèel**: Automatizace vytváøení kontejnerù s pøedem pøipravenými závislostmi, konfigurací a kódem.  

---

### **2. Hlavní typy souborù**  

#### **a) Dockerfile**  

- **Popis**: Textový soubor s instrukcemi pro sestavení image.  
- **Klíèové instrukce pro Windows**:  
  ```dockerfile  
  FROM mcr.microsoft.com/windows/servercore:ltsc2022  # Base image  
  COPY ./app C:/app                                  # Kopírování souborù  
  RUN powershell Install-WindowsFeature Web-Server   # Instalace komponent  
  CMD ["dotnet", "MojeAplikace.dll"]                # Výchozí pøíkaz  
  ```  

#### **b) docker-compose.yml**  

- **Popis**: Definice více služeb (kontejnerù) a jejich vztahù.  
- **Pøíklad pro Windows**:  
  ```yaml  
  version: "3.9"  
  services:  
    web:  
      image: mcr.microsoft.com/dotnet/aspnet:6.0  
      ports:  
        - "8080:80"  
    db:  
      image: mcr.microsoft.com/mssql/server:2022-latest  
      environment:  
        SA_PASSWORD: "Heslo123!"  
  ```  

#### **c) .dockerignore**  

- **Popis**: Urèuje soubory/složky, které se **nekopírují** do build kontextu.  
- **Pøíklad**:  
  ```  
  # .dockerignore  
  Dockerfile  
  **/*.log  
  tmp/  
  ```  

---

### **3. Specifika pro Windows**  

#### **a) Cesty a formátování**  

- Používejte **backslash** (`\`) nebo **forward slash** (`/`) pro cesty:  
  ```dockerfile  
  COPY ./app C:/app    # Správnì  
  COPY ./app C:\app    # Správnì  
  ```  
- **Case-insensitive**: Názvy souborù nezávisí na velikosti písmen.  

#### **b) Base Image**  

- **Windows Server Core**: Plná verze pro .NET Framework, IIS.  
- **Nano Server**: Ultra-lehký (vhodný pro .NET Core).  
- **Dùležité**: Verze OS v image musí odpovídat hostiteli (napø. `ltsc2022`).  

#### **c) PowerShell vs. CMD**  

- Výchozí shell je `cmd`; pro PowerShell použijte:  
  ```dockerfile  
  SHELL ["powershell", "-Command"]  
  RUN Install-WindowsFeature Web-Server  
  ```  

---

### **4. Pøíkazy pro sestavení**  

- **Sestavení image z Dockerfile**:  
  ```powershell  
  docker build -t mùj-image:tag .  
  ```  
- **Sestavení s využitím cache**:  
  ```powershell  
  docker build --no-cache -t mùj-image .  # Ignoruje cache  
  ```  
- **Multi-stage build**:  
  ```dockerfile  
  # Fáze 1: Build  
  FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build  
  COPY . .  
  RUN dotnet publish -c Release -o /app  

  # Fáze 2: Runtime  
  FROM mcr.microsoft.com/windows/servercore:ltsc2022  
  COPY --from=build /app /app  
  ```  

---

### **5. Best Practices**  

1. **Minimalizujte poèet vrstev**: Kombinujte pøíkazy `RUN` pomocí `&&`.  
2. **Optimalizujte build kontext**: Používejte `.dockerignore`.  
3. **Verzování**: Vždy specifikujte tagy (napø. `:v1`, `:ltsc2022`).  
4. **Testujte v izolovaném prostøedí**: Ovìøte kompatibilitu s hostitelem.  

---

### **6. Èasté problémy a øešení**  

| **Problém** | **Pøíèina** | **Øešení** |  
|-------------|-------------|-------------|  
| *"COPY failed"* | Neplatná cesta nebo práva. | Ovìøte formát cest a `.dockerignore`. |  
| *"Image too large"* | Nadbyteèné soubory ve vrstvách. | Použijte multi-stage build a èistìte doèasné soubory. |  
| *"Container exits immediately"* | Chybí dlouho bìžící proces. | Pøidejte `CMD` s hlavní službou (napø. `dotnet run`). |  
| *"OS version mismatch"* | Nesoulad tagu base image a hostitele. | Zmìòte tag na odpovídající (napø. `ltsc2022`). |  

---

### **7. Užiteèné nástroje**  

- **Visual Studio**: Automatické generování Dockerfile pro .NET aplikace.  
- **Docker Desktop Dashboard**: Pøehledná správa image a kontejnerù.  
- **dive**: Analýza vrstev image pro optimalizaci velikosti.  

---

### **8. Odkazy a zdroje** 

- **Dokumentace**:  
  - [Microsoft – Windows Container Dockerfile](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/manage-docker/manage-windows-dockerfile)  
  - [Docker – Oficiální dokumentace](https://docs.docker.com/engine/reference/builder/)  
- **Komunita**: Fóra Stack Overflow, GitHub repozitáøe.  

---

**Shrnutí**: Sestavovací soubory pro Docker na Windows vyžadují pozornost k formátování cest, výbìru base imagí a kompatibilitì verzí OS. Klíèové je využití multi-stage builds, minimalizace vrstev a správné nastavení `.dockerignore`. Pro komplexní aplikace použijte `docker-compose.yml`.
