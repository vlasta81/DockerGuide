
# Docker – vrstvení a cachování imagí (Windows)

---

### **1. Vrstvení (Layering) Docker Imagí**  

- **Image** se skládá z **vrstev** (layers), kde každý příkaz v Dockerfile vytvoří novou vrstvu.  
- **Princip**:  
  - Každá vrstva je **neměnná** (immutable) a ukládá se do cache.  
  - Při rebuildování se obnoví pouze změněné vrstvy.  
- **Příklad vrstvení v Dockerfile**:  
  ```dockerfile
  FROM mcr.microsoft.com/windows/servercore:ltsc2022  # Vrstva 1: Base image
  COPY app/ C:\app                                    # Vrstva 2: Kopírování souborů
  RUN powershell Install-WindowsFeature Web-Server    # Vrstva 3: Instalace komponent
  ```  

---

### **2. Cachování vrstev na Windows**  

- **Cache** = Ukládání vrstev do dočasné paměti pro urychlení budoucích buildů.  
- **Kdy se cache použije**:  
  - Pokud se nezmění **instrukce v Dockerfile** a soubory v kontextu buildu (např. `COPY` nebo `ADD`).  
- **Kdy se cache zruší**:  
  - Změna libovolné instrukce **nad aktuální vrstvou**.  
  - Změna souborů v adresáři kopírovaném do image (např. `COPY . .`).  

---

### **3. Specifika pro Windows**  

- **Velikost vrstev**:  
  - Windows base image (např. `servercore`) mají **větší velikost** (až 5 GB).  
  - Dopad: Pomalé stahování a rebuildování.  
- **Optimalizace**:  
  - Používejte **minimalizované base image** (např. `nanoserver`).  
  - Kombinujte příkazy `RUN` do jednoho řádku, abyste snížili počet vrstev:  
    ```dockerfile
    RUN powershell -Command \
        Install-WindowsFeature Web-Server && \
        Remove-Item -Recurse C:\Temp\*  
    ```  

---

### **4. Best Practices pro Windows**  

#### **a) Řazení instrukcí v Dockerfile**  

1. Umístěte **méně časté změny nahoře** (např. `FROM`, instalace balíčků).  
2. **Časté změny (např. `COPY . .`) až dole** – minimalizuje ztrátu cache.  

#### **b) Multi-stage Builds**  

- Snižte velikost finální image kopírováním pouze potřebných souborů z mezistadií:  
  ```dockerfile
  # Fáze 1: Build
  FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
  COPY . .
  RUN dotnet publish -c Release -o /app

  # Fáze 2: Runtime
  FROM mcr.microsoft.com/windows/servercore:ltsc2022
  COPY --from=build /app /app
  ```  

#### **c) Čištění dočasných souborů**  

- Odstraňte nepotřebné soubory **ve stejné vrstvě**, kde vznikly:  
  ```dockerfile
  RUN powershell -Command \
      curl -OutFile installer.exe https://example.com/installer.exe && \
      Start-Process installer.exe -Wait && \
      Remove-Item installer.exe  
  ```  

---

### **5. Nástroje pro analýzu vrstev**  

| Příkaz | Popis |  
|--------|-------|  
| `docker history <image>` | Zobrazí vrstvy image a jejich velikost. |  
| `docker build --no-cache` | Vynutí úplný rebuild bez cache. |  
| `docker image inspect <image>` | Detaily o image (včetně vrstev). |  

---

### **6. Časté problémy a řešení**  

- **Problém**: Zbytečně velká image kvůli dočasným souborům.  
  - **Řešení**: Čistěte soubory ve stejné vrstvě, kde vznikly.  
- **Problém**: Časté ztráty cache při `COPY . .`.  
  - **Řešení**: Použijte `.dockerignore` pro vyloučení nepotřebných souborů.  
  ```dockerignore
  # .dockerignore soubor
  Dockerfile
  **/*.log
  tmp/
  ```  

---

### **7. Příklad: Optimalizovaný Dockerfile pro Windows**  

```dockerfile
# Base image s minimální velikostí
FROM mcr.microsoft.com/windows/nanoserver:ltsc2022

# Instalace závislostí v jedné vrstvě
RUN powershell -Command \
    Install-PackageProvider -Name NuGet -Force && \
    Install-Module -Name PSScriptAnalyzer -Force

# Kopírování aplikace (změny zde invalidují cache níže)
WORKDIR /app
COPY ./publish .

# Definice vstupního bodu
CMD ["pwsh", "-File", "/app/start.ps1"]
```  

---

### **8. Výhody a omezení cachování**

| **Výhody** | **Omezení** |  
|------------|-------------|  
| Rychlejší buildy. | Velikost cache může zabírat místo na disku. |  
| Opakovatelnost buildů. | Nutnost správného řazení instrukcí. |  
| Efektivní správa závislostí. | Omezená účinnost při častých změnách kódu. |  

---

### **9. Užitečné zdroje**  

- **Dokumentace**:  
  - [Docker – Layer caching](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)  
  - [Microsoft – Windows Container Best Practices](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/manage-containers/container-base-images)  
- **Nástroje**:  
  - **Docker Desktop** (přehled vrstev v záložce *Images*).  
  - **dive** (analýza efektivity vrstev).  

---

**Shrnutí**: Vrstvení a cachování imagí jsou klíčové pro efektivní práci s Dockerem na Windows. Díky optimalizaci Dockerfile (minimalizace vrstev, multi-stage builds) a využití cache lze výrazně urychlit vývojový cyklus. Pozor na velikost Windows imagí a správu dočasných souborů!
