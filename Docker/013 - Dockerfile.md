
# Dockerfile (Windows)

---

### **1. Co je Dockerfile?**  

- **Textový skript** s instrukcemi pro automatizované sestavení Docker image.  
- Obsahuje kroky jako instalace závislostí, kopírování souborů a konfiguraci prostředí.  
- **Výhody**: Reprodukovatelnost, snadné nasazování a správa aplikací v kontejnerech.  

---

### **2. Specifika Dockerfile pro Windows**  

#### **a) Base Image**  

- **Windows Server Core**:  
  - Pro aplikace vyžadující plnou funkcionalitu Windows (např. .NET Framework, IIS).  
  - Příklad: `FROM mcr.microsoft.com/windows/servercore:ltsc2022`.  
- **Nano Server**:  
  - Ultra-lehký image pro moderní aplikace (např. .NET Core).  
  - Příklad: `FROM mcr.microsoft.com/windows/nanoserver:ltsc2022`.  

#### **b) Kompatibilita OS**  

- **Tagy verzí**: Musí odpovídat verzi Windows hostitele (např. `ltsc2019`, `ltsc2022`).  
- Chyba: *"The container operating system does not match the host"* → Změňte tag v `FROM`.  

---

### **3. Klíčové instrukce v Dockerfile**  

| **Instrukce** | **Popis** | **Příklad pro Windows** |  
|---------------|-----------|--------------------------|  
| `FROM`        | Určuje základní image. | `FROM mcr.microsoft.com/dotnet/aspnet:6.0` |  
| `COPY`        | Kopíruje soubory z hostitele do image. | `COPY ./app C:/app` |  
| `RUN`         | Spouští příkazy při buildování. | `RUN powershell Install-WindowsFeature Web-Server` |  
| `WORKDIR`     | Nastaví pracovní adresář. | `WORKDIR C:/app` |  
| `CMD`         | Definuje výchozí příkaz pro spuštění kontejneru. | `CMD ["dotnet", "MojeAplikace.dll"]` |  
| `SHELL`       | Změní výchozí shell (např. PowerShell). | `SHELL ["powershell", "-Command"]` |  

---

### **4. Best Practices pro Windows**  

#### **a) Minimalizace velikosti image**  

- Používejte **Nano Server** místo Server Core, pokud je to možné.  
- Kombinujte příkazy `RUN` do jedné vrstvy:  
  ```dockerfile  
  RUN powershell -Command \  
      Install-WindowsFeature Web-Server && \  
      Remove-Item C:/Windows/Temp/* -Recurse  
  ```  

#### **b) Bezpečnost**  

- **Aktualizujte base image**: Zabráníte zranitelnostem.  
- **Nepoužívejte `latest` tag**: Explicitně specifikujte verzi (např. `ltsc2022`).  
- **Omezení práv**: V produkci nepoužívejte `ContainerAdministrator`.  

#### **c) Optimalizace cache**  

- Umístěte **méně časté změny nahoře** (např. instalace balíčků).  
- **Časté změny (např. `COPY . .`) až dole**.  

---

### **5. Příklad Dockerfile pro ASP.NET**  

```dockerfile  
# Fáze 1: Sestavení aplikace  
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build  
WORKDIR /src  
COPY . .  
RUN dotnet publish -c Release -o /app  

# Fáze 2: Runtime  
FROM mcr.microsoft.com/windows/servercore:ltsc2022  
WORKDIR /app  
COPY --from=build /app .  
CMD ["dotnet", "MojeAplikace.dll"]  
```  

---

### **6. Časté problémy a řešení**  

| **Problém** | **Příčina** | **Řešení** |  
|-------------|-------------|-------------|  
| *"Access denied"* při kopírování | Nedostatečná práva k cestě. | Použijte `WORKDIR` před `COPY`. |  
| Velikost image přes 10 GB | Nadbytečné soubory v image. | Odstraňte dočasné soubory v `RUN`. |  
| Kontejner se ukončí okamžitě | Chybí dlouho běžící proces. | Použijte `CMD` s persistentní službou (např. `ping localhost -t`). |  

---

### **7. Rozdíly oproti Linux Dockerfile**  

- **Cesty**: Používejte **backslash** (`C:/app`) nebo **forward slash** (`C:\app`).  
- **Case-insensitive**: Názvy souborů nezáleží na velikosti písmen.  
- **SHELL**: Výchozí je `cmd`; pro PowerShell přepněte pomocí `SHELL ["powershell", "-Command"]`.  

---

### **8. Užitečné zdroje**  

- **Dokumentace**:  
  - [Microsoft – Windows Container Dockerfile](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/manage-docker/manage-windows-dockerfile)  
  - [Docker – Oficiální reference](https://docs.docker.com/engine/reference/builder/)  
- **Nástroje**:  
  - **Visual Studio** (generování Dockerfile pro .NET aplikace).  
  - **Docker Extension pro VS Code** (syntax highlighting, linting).  

---

**Shrnutí**: Dockerfile pro Windows vyžaduje specifický přístup kvůli velikosti imagí a kompatibilitě OS. Klíčové je využití optimálních base imagí, minimalizace vrstev a správné řízení oprávnění. Pro složité aplikace preferujte **multi-stage builds** a vždy testujte image v izolovaném prostředí.
