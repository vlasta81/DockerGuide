
# Volumes (Windows)

---

### **1. Úvod**  

Docker Volumes (svazky) jsou preferovaným způsobem pro **trvalé ukládání dat** v Dockeru. Umožňují oddělit data od životního cyklu kontejneru, což je klíčové pro aplikace jako databáze, webové servery nebo mikroslužby. Na Windows má práce s volumes specifické vlastnosti kvůli odlišnostem v souborovém systému a integraci s WSL2 (pro Linux kontejnery).

---

### **2. Co je Docker Volume?**  

- **Definice**: Správce Dockeru vytvořené úložiště, které existuje nezávisle na kontejnerech.  
- **Klíčové vlastnosti**:  
  - Data přežívají odstranění/restart kontejneru.  
  - Lze je sdílet mezi více kontejnery.  
  - Podpora šifrování a cloudových úložišť (např. Azure File Storage).  
- **Rozdíl oproti Bind Mounts**:  
  - Volumes jsou plně spravovány Dockerem, zatímco bind mounts mapují hostitelské složky.  
  - Volumes jsou bezpečnější (izolovanější přístup).  

---

### **3. Vytváření a správa volumes na Windows**  

#### **a) Základní příkazy (PowerShell)**  

```powershell
# Vytvoření svazku
docker volume create muj_svazek

# Spuštění kontejneru s připojeným svazkem
docker run -d --name web -v muj_svazek:/app/data nginx

# Zobrazení seznamu svazků
docker volume ls

# Odstranění svazku
docker volume rm muj_svazek

# Prohlížení metadat svazku
docker volume inspect muj_svazek
```  

#### **b) Kam se ukládají data?**  

- **Pro Linux kontejnery na WSL2**:  
  Data jsou ve virtuálním filesystému WSL2 (cesta: `\\wsl$\docker-desktop-data\version-pack-data\community\docker\volumes`).  
- **Pro Windows kontejnery**:  
  Data jsou v adresáři Dockeru na hostiteli (obvykle `C:\ProgramData\Docker\volumes`).  

---

### **4. Specifika pro Windows**  

#### **a) Typy volume driverů**  

- **`local`**: Výchozí driver pro ukládání na lokální disk.  
- **`azure_file`**: Připojení Azure File Storage jako svazku (použitelné pro cloudové aplikace).  
- **3rd-party drivery**: Např. Portworx, NetApp (vyžadují konfiguraci).  

#### **b) Formát cest**  

- **Unix-like formát v kontejneru**:  
  ```powershell
  docker run -v muj_svazek:/cesta/v/kontejneru
  ```  
- **Windows cesty pro bind mounts**:  
  ```powershell
  docker run -v C:\HostSlozka:/cesta/v/kontejneru
  ```  

#### **c) Omezení**  

- **Práva k souborům**:  
  - Problémy s přístupem mohou nastat, pokud kontejner běží pod jiným uživatelem než hostitel.  
  - Řešení: Nastavte sdílení složek v **Docker Desktopu** (Settings → Resources → File Sharing).  
- **SMB/CIFS pro síťové svazky**:  
  ```powershell
  docker volume create --driver local `
    --opt type=cifs `
    --opt device=//server/share `
    --opt username=user --opt password=heslo `
    sitovy_svazek
  ```  

---

### **5. Praktické příklady**  

#### **a) SQL Server s trvalým svazkem**  

```powershell
# Vytvoření svazku
docker volume create sql_data

# Spuštění SQL Serveru
docker run -d --name sql-server `
  -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Heslo123!" `
  -v sql_data:/var/opt/mssql `
  mcr.microsoft.com/mssql/server:2019-latest
```  

#### **b) Sdílení svazku mezi kontejnery**  

```powershell
# Kontejner 1 (zápis dat)
docker run -d --name writer -v shared_data:/data alpine sh -c "echo 'Ahoj!' > /data/test.txt"

# Kontejner 2 (čtení dat)
docker run -it --name reader -v shared_data:/data alpine cat /data/test.txt
```  

#### **c) Docker Compose s volume**  

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

### **6. Řešení problémů**  

- **Kontejner nemá přístup k volume**:  
  - Ověřte, zda je svazek vytvořen (`docker volume ls`).  
  - Zkontrolujte práva v Docker Desktopu (File Sharing).  
- **Data nejsou trvalá**:  
  - Ujistěte se, že data ukládáte do připojeného svazku, ne do dočasného filesystému kontejneru.  
- **Chyby při připojování SMB/CIFS**:  
  - Ověřte síťové připojení a přihlašovací údaje.  

---

### **7. Alternativy k volumes**  

- **Bind Mounts**: Vhodné pro vývoj, ale závislé na hostitelském systému.  
- **tmpfs**: Dočasné úložiště v paměti (pouze pro Linux kontejnery na WSL2).  

---

### **8. Shrnutí**  

- **Volumes** jsou optimální pro:  
  - Produkční prostředí.  
  - Sdílení dat mezi kontejnery.  
  - Cloudová úložiště (Azure File Storage).  
- **Windows specifika**:  
  - Pozor na formát cest a práva k souborům.  
  - Pro Linux kontejnery využijte WSL2 integraci.  
  - Pro síťové svazky použijte SMB/CIFS.  

**Doporučení**:  
- Pro komplexní aplikace kombinujte **Docker Compose** s volumes.  
- Pravidelně čistěte nepoužívané svazky příkazem `docker volume prune`.  
- Testujte práva přístupu k svazkům již během vývoje.  

--- 

**Viz také**: Docker dokumentace – [Manage data in Docker (Windows)](https://docs.docker.com/storage/volumes/).
