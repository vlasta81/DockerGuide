
# Ukládání dat a trvalá data (Windows)

---

### **1. Úvod**  

Kontejnery Dockeru jsou dočasné (ephemeral) – po odstranění kontejneru se ztratí všechna data uvnitř něj. Pro **trvalé ukládání dat** (persistence) se používají **Docker volumes** a **bind mounts**, které oddělují data od životního cyklu kontejneru. Na Windows je práce s daty specifická kvůli odlišnostem v souborových systémech a síťových cestách.

---

### **2. Metody pro trvalé ukládání dat**  

#### **a) Docker Volumes**  

- **Definice**: Spravované úložiště vytvořené a řízené Dockerem. Ideální pro cloudová řešení a sdílení dat mezi kontejnery.  
- **Výhody**:  
  - Nezávislost na hostitelském OS.  
  - Možnost migrace mezi kontejnery a hostiteli.  
  - Podpora šifrování (pro Windows Server 2019+).  
- **Příkazy (PowerShell)**:  
  ```powershell
  # Vytvoření volume
  docker volume create muj_volume

  # Připojení volume ke kontejneru
  docker run -d --name muj_kontejner -v muj_volume:/data/cesta microsoft/iis

  # Seznam všech volumes
  docker volume ls

  # Odstranění volume
  docker volume rm muj_volume
  ```  

#### **b) Bind Mounts**  

- **Definice**: Připojení konkrétní složky z hostitelského systému do kontejneru.  
- **Výhody**:  
  - Rychlý vývoj (např. mapování zdrojového kódu).  
  - Přístup k hostitelským souborům.  
- **Rizika**:  
  - Závislost na struktuře hostitelského systému.  
  - Bezpečnostní problémy (práva k souborům).  
- **Příklad**:  
  ```powershell
  docker run -d --name web -v C:\MojeData:/app/data nginx
  ```  

#### **c) tmpfs Mounts**  

- **Definice**: Dočasné úložiště v paměti (RAM). Data se ztratí po odstranění kontejneru.  
- **Použití**: Citlivá data (např. dočasné klíče).  
- **Windows omezení**: Funguje pouze pro Linux kontejnery na WSL2.  

---

### **3. Specifika pro Windows**  

1. **Cesty k souborům**:  
   - Používejte **Unix-like formát** v kontejnerech (např. `/app/data`).  
   - Pro bind mounts na Windows:  
     - Absolutní cesty: `C:\MojeSlozka` → mapování na `/data` v kontejneru.  
     - Problémy s právy: Kontejnery často potřebují přístup k síti **bez omezení** (User Account Control).  

2. **Typy kontejnerů**:  
   - **Windows kontejnery**: Podporují NTFS a síťové cesty (SMB/CIFS).  
   - **Linux kontejnery na Windows**: Vyžadují WSL2 a používají virtuální filesystem.  

3. **Hyper-V Isolation**:  
   - Umožňuje lepší izolaci, ale může komplikovat přístup k hostitelským diskům.  

4. **Named Pipes**:  
   - Pro komunikaci s Windows službami (např. Docker volume pro SQL Server):  
     ```powershell
     docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=Heslo123!" `
                -v \\.\pipe\docker_engine:\\.\pipe\docker_engine `
                mcr.microsoft.com/mssql/server:2019-latest
     ```  

---

### **4. Praktické příklady**  

#### **a) SQL Server s trvalým volume**  

```powershell
# Vytvoření volume
docker volume create sql_data

# Spuštění SQL Serveru s volume
docker run -d --name sql-server `
           -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Heslo123!" `
           -v sql_data:/var/opt/mssql `
           mcr.microsoft.com/mssql/server:2019-latest
```  

#### **b) ASP.NET aplikace s bind mount**  

```powershell
# Mapování složky s konfiguračními soubory
docker run -d --name web-app `
           -v C:\MojeAplikace\config:/app/config `
           moje-aspnet-app
```  

---

### **5. Řešení problémů**  

- **Práva k souborům**:  
  - Pokud kontejner nemůže zapisovat do složky, nastavte **sdílení v Docker Desktopu** (Settings → Resources → File Sharing).  
- **Kontrola připojených volumes**:  
  ```powershell
  docker inspect muj_kontejner --format "{{ json .Mounts }}"
  ```  
- **Smazání nepoužívaných volumes**:  
  ```powershell
  docker volume prune
  ```  

---

### **6. Alternativní řešení pro Windows**  

- **Azure File Storage**: Připojení cloudového úložiště jako SMB sdílení.  
  ```powershell
  docker run -d --name web `
             --mount type=volume,source=azure_volume,target=/app/data,volume-driver=azure_file `
             moje-aplikace
  ```  
- **Docker Compose pro správu volumes**:  
  ```yaml
  version: '3.8'
  services:
    web:
      image: nginx
      volumes:
        - web_data:/usr/share/nginx/html
  volumes:
    web_data:
  ```  

---

### **7. Shrnutí**  

- **Volumes**: Nejlepší volba pro trvalá data (nezávislá na kontejnerech).  
- **Bind Mounts**: Vhodné pro vývoj a přímý přístup k hostitelským souborům.  
- **Windows specifika**:  
  - Formát cest (`C:\Data` vs. `/data`).  
  - Omezení při práci s Linux kontejnery (WSL2).  
  - Integrace s Windows službami (např. SQL Server přes named pipes).  

**Doporučení**: Pro produkci používejte **Docker volumes** a cloudová řešení (Azure File Storage). Při vývoji využijte **bind mounts** pro rychlou iteraci.
