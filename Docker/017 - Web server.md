
# Web server (Windows)

---

### **1. Úvod do webových serverů v Dockeru na Windows**  

- **Webový server v kontejneru**: Izolované prostředí pro běh služeb jako **IIS**, **Nginx**, **Apache** nebo **ASP.NET aplikací**.  
- **Výhody**:  
  - Konzistence mezi vývojovým a produkčním prostředím.  
  - Snadné škálování a nasazování.  
  - Možnost spouštět různé verze serverů současně.  

---

### **2. Klíčové komponenty**  

#### **a) Base Image pro Windows**  

- **IIS (Internet Information Services)**:  
  - Oficiální image od Microsoftu: `mcr.microsoft.com/windows/servercore/iis`.  
  - Příklad použití:  
    ```powershell  
    docker run -d -p 80:80 mcr.microsoft.com/windows/servercore/iis  
    ```  
- **ASP.NET**:  
  - Image pro .NET Framework: `mcr.microsoft.com/dotnet/framework/aspnet`.  
  - Image pro .NET Core: `mcr.microsoft.com/dotnet/aspnet:6.0`.  

#### **b) Konfigurace** 

- **Mapování portů**: `-p <host_port>:<container_port>`.  
- **Sdílení souborů**: Pro nasazení webového obsahu použijte `COPY` v Dockerfile nebo `-v` pro bind mount.  

---

### **3. Vytvoření webového serveru v kontejneru**  

#### **Krok 1: Dockerfile pro IIS**  

```dockerfile  
# Base image s IIS  
FROM mcr.microsoft.com/windows/servercore/iis:ltsc2022  

# Kopírování webového obsahu  
COPY ./web-content C:/inetpub/wwwroot  

# Exponování portu 80  
EXPOSE 80  
```  

#### **Krok 2: Sestavení a spuštění**  

```powershell  
docker build -t můj-web .  
docker run -d -p 8080:80 můj-web  
```  
- Web bude dostupný na `http://localhost:8080`.  

---

### **4. Příklady použití**  

#### **a) Spuštění Nginx na Windows**  

- Použijte Linuxový kontejner přes **WSL 2**:  
  ```powershell  
  docker run -d -p 80:80 --name nginx nginx:latest  
  ```  

#### **b) ASP.NET aplikace v kontejneru** 

1. Vytvořte Dockerfile:  
   ```dockerfile  
   FROM mcr.microsoft.com/dotnet/aspnet:6.0  
   WORKDIR /app  
   COPY ./publish .  
   ENTRYPOINT ["dotnet", "MojeAplikace.dll"]  
   ```  
2. Spusťte:  
   ```powershell  
   docker run -d -p 5000:80 můj-dotnet-app  
   ```  

---

### **5. Časté problémy a řešení**  

| **Problém** | **Příčina** | **Řešení** |  
|-------------|-------------|-------------|  
| *"Service Unavailable"* (HTTP 503) | IIS není správně nakonfigurován. | Ověřte logy (`docker logs <ID>`) a nastavení aplikace v kontejneru. |  
| *"Port conflict"* | Port 80 je již obsazen. | Změňte hostitelský port (`-p 8080:80`). |  
| *"Access denied"* u souborů | Nedostatečná práva k složce. | Povolte sdílení složky v Docker Desktopu (**Settings → Resources → File Sharing**). |  
| *"Container OS mismatch"* | Verze OS image neodpovídá hostiteli. | Stáhněte image se správným tagem (např. `ltsc2022`). |  

---

### **6. Best Practices**  

1. **Minimalizujte image**:  
   - Pro .NET Core použijte image `mcr.microsoft.com/dotnet/aspnet`, ne `servercore`.  
2. **Používejte .dockerignore**: Vynechte nepotřebné soubory (např. `bin/`, `obj/`).  
3. **Zálohujte data**: Pro trvalý webový obsah použijte **Docker volumes** nebo **bind mounts**.  
4. **Bezpečnost**:  
   - Aktualizujte base image.  
   - Nepoužívejte výchozí administrátorský účet (`ContainerAdministrator`).  

---

### **7. Nástroje a monitoring**  

- **Docker Desktop Dashboard**: Přehled běžících kontejnerů a jejich prostředků.  
- **Logování**:  
  ```powershell  
  docker logs <ID> --tail 100  # Zobrazí posledních 100 řádků logu  
  ```  
- **Analýza výkonu**: `docker stats` pro sledování CPU, RAM a sítě.  

---

### **8. Užitečné zdroje**  

- **Dokumentace**:  
  - [Microsoft – IIS v kontejneru](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/quick-start/iis-quickstart)  
  - [Docker – Oficiální dokumentace](https://docs.docker.com/)  
- **Komunita**:  
  - Stack Overflow (tagy `docker`, `windows-containers`).  
  - GitHub repozitáře s ukázkovými projekty.  

---

**Shrnutí**: Webové servery v Dockeru na Windows umožňují rychlé nasazení a izolaci služeb jako IIS, Nginx nebo ASP.NET aplikací. Klíčové je správné mapování portů, volba kompatibilních imagí a dodržování bezpečnostních standardů. Pro produkční prostředí preferujte Linuxové kontejnery přes WSL 2 (vyšší výkon) a automatizujte správu pomocí Docker Compose.
