
# Docker – práce s kontejnery (Windows)

---

### **1. Základy práce s kontejnery**  

- **Kontejner** = Izolovaný běhový prostředek vytvořený z Docker image.  
- **Životní cyklus**:  
  1. Vytvoření (`docker run`).  
  2. Běh (`docker start/stop`).  
  3. Odstranění (`docker rm`).  
- **Klíčové vlastnosti**:  
  - Izolace od hostitelského systému.  
  - Rychlé spouštění a replikace.  

---

### **2. Práce s kontejnery na Windows**  

#### **a) Spouštění kontejnerů**  

- **Základní příkaz**:  
  ```powershell  
  docker run [parametry] <image>  
  ```  
- **Parametry**:  
  - `-d`: Spustí kontejner na pozadí (*detached mode*).  
  - `-it`: Interaktivní režim (např. pro PowerShell).  
  - `--name`: Pojmenování kontejneru.  
  - `-p <host_port>:<container_port>`: Mapování portů.  

#### **b) Příklady**  

- Spustit Windows Server Core s PowerShell:  
  ```powershell  
  docker run -it mcr.microsoft.com/windows/servercore:ltsc2022 pwsh  
  ```  
- Spustit webový server IIS:  
  ```powershell  
  docker run -d -p 80:80 --name můj-iis mcr.microsoft.com/windows/servercore/iis  
  ```  

---

### **3. Správa běžících kontejnerů**  

#### **a) Klíčové příkazy**  

| Příkaz | Popis |  
|--------|-------|  
| `docker ps` | Zobrazí **běžící** kontejnery. |  
| `docker ps -a` | Zobrazí **všechny** kontejnery (včetně zastavených). |  
| `docker stop <ID>` | Zastaví kontejner. |  
| `docker start <ID>` | Spustí zastavený kontejner. |  
| `docker restart <ID>` | Restartuje kontejner. |  
| `docker rm <ID>` | Smaže kontejner. |  
| `docker logs <ID>` | Zobrazí logy kontejneru. |  

#### **b) Interakce s běžícím kontejnerem** 

- Připojení ke kontejneru:  
  ```powershell  
  docker exec -it <ID> cmd  # Příkazový řádek  
  docker exec -it <ID> pwsh  # PowerShell  
  ```  

---

### **4. Síťování a svazky (volumes)**  

#### **a) Síťové režimy**  

- **Bridge**: Výchozí síť pro komunikaci mezi kontejnery.  
- **Host**: Kontejner sdílí síťový stack s hostitelem.  
- **Vlastní sítě**:  
  ```powershell  
  docker network create můj-síť  
  docker run --network můj-síť ...  
  ```  

#### **b) Práce se svazky**  

- **Bind mount**: Propojení složky hostitele a kontejneru.  
  ```powershell  
  docker run -v C:\Data:D:\Data <image>  
  ```  
- **Docker volumes**: Trvalá data spravovaná Dockerem.  
  ```powershell  
  docker volume create můj-svazek  
  docker run -v můj-svazek:D:\Data <image>  
  ```  

---

### **5. Windows vs. Linux kontejnery**  

- **Windows kontejnery**:  
  - Vyžadují shodu **verze OS** (např. `ltsc2022`).  
  - Větší velikost (base image ~5 GB).  
  - Vhodné pro .NET Framework, IIS, SQL Server.  
- **Linux kontejnery**:  
  - Běží přes **WSL 2** (lepší výkon než Hyper-V).  
  - Menší velikost, širší podpora open-source nástrojů.  

---

### **6. Časté problémy a řešení**  

- **Chyba: *"Container OS mismatch"***  
  - **Příčina**: Image neodpovídá verzi Windows hostitele.  
  - **Řešení**: Stáhněte image se správným tagem (např. `ltsc2022`).  
- **Konflikt portů**  
  - **Příklad**: `Error: Port 80 is already in use`.  
  - **Řešení**: Změňte hostitelský port (`-p 8080:80`).  
- **Práva k souborům ve svazcích**  
  - **Řešení**: V Docker Desktopu povolte sdílení složky (**Settings → Resources → File Sharing**).  

---

### **7. Best Practices**  

1. **Jedna služba = jeden kontejner** (např. web server + databáze jako dva kontejnery).  
2. **Používejte oficiální image** (např. od Microsoftu).  
3. **Aktualizujte base image** kvůli bezpečnostním záplatám.  
4. **Logování**: Ukládejte logy do externích systémů (např. ELK stack).  
5. **Orchestrace**: Pro složité aplikace použijte **Docker Compose** nebo **Kubernetes**.  

---

### **8. Příklady použití**  

#### **a) .NET aplikace v kontejneru**  

1. Vytvořte Dockerfile pro ASP.NET:  
   ```dockerfile  
   FROM mcr.microsoft.com/dotnet/aspnet:6.0  
   COPY ./app /app  
   WORKDIR /app  
   ENTRYPOINT ["dotnet", "MojeAplikace.dll"]  
   ```  
2. Sestavte a spusťte:  
   ```powershell  
   docker build -t můj-dotnet-app .  
   docker run -p 5000:80 můj-dotnet-app  
   ```  

#### **b) SQL Server v kontejneru** 

```powershell  
docker run -d -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Heslo123!" -p 1433:1433 --name sql-server mcr.microsoft.com/mssql/server:2022-latest  
```  

---

### **9. Nástroje a zdroje**  

- **Docker Desktop Dashboard**: Přehled běžících kontejnerů a jejich stavů.  
- **Visual Studio**: Integrovaná podpora pro Docker (automatické generování Dockerfile).  
- **Dokumentace**:  
  - [Microsoft – Windows kontejnery](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/)  
  - [Docker – Oficiální příručka](https://docs.docker.com/get-started/)  

---

**Tip**: Pro efektivní správu kontejnerů používejte **aliasy v PowerShellu** (např. `alias dps='docker ps -a'`).
