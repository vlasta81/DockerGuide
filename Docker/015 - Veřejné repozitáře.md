
# Veøejné repozitáøe (Windows)

---

### **1. Co jsou veøejné repozitáøe?**  

- **Cloudová úložištì** pro sdílení a distribuci Docker imagí.  
- **Využití**:  
  - Stažení hotových imagí (napø. databáze, webové servery).  
  - Sdílení vlastních imagí s komunitou.  
- **Klíèové vlastnosti**:  
  - Veøejný pøístup (bez nutnosti autentizace).  
  - Podpora tagování, verzování a automatických buildù.  

---

### **2. Hlavní veøejné repozitáøe pro Windows**  

#### **a) Docker Hub**  

- **Výchozí registry** pro Docker komunitu.  
- **Oficiální Windows image**:  
  - Od Microsoftu (napø. `mcr.microsoft.com/windows/servercore`, `mcr.microsoft.com/dotnet/framework`).  
  - Vyžadují správný **tag verze OS** (napø. `ltsc2022`).  
- **Omezení**:  
  - Bezplatné úèty mají **rate limits** (100 stažení/6 hodin pro anonymní uživatele).  

#### **b) Microsoft Container Registry (MCR)**  

- Oficiální registry Microsoftu pro Windows a .NET image.  
- **Adresa**: `mcr.microsoft.com`.  
- **Pøíklady**:  
  - `mcr.microsoft.com/windows/servercore:ltsc2022`  
  - `mcr.microsoft.com/dotnet/aspnet:6.0`  

#### **c) GitHub Container Registry (GHCR)**  

- Veøejné repozitáøe hostované na GitHubu.  
- **Výhody**: Integrace s GitHub Actions, automatické buildy z kódu.  

---

### **3. Práce s veøejnými repozitáøi na Windows**  

#### **a) Stažení image (Pull)**  

```powershell  
docker pull mcr.microsoft.com/windows/servercore:ltsc2022  
docker pull nginx  # Implicitnì z Docker Hubu  
```  

#### **b) Nahrání vlastní image (Push)**  

1. Oznaète image tagem podle veøejného repozitáøe:  
   ```powershell  
   docker tag mùj-image:verce uživatel/mùj-image:verze  
   ```  
2. Nahrajte do Docker Hubu:  
   ```powershell  
   docker push uživatel/mùj-image:verze  
   ```  

#### **c) Hledání imagí**  

- **Pøímo v CLI**:  
  ```powershell  
  docker search microsoft/dotnet  
  ```  
- **Webové rozhraní**: [Docker Hub](https://hub.docker.com/), [MCR](https://mcr.microsoft.com/).  

---

### **4. Výhody a rizika veøejných repozitáøù**  

| **Výhody** | **Rizika** |  
|------------|------------|  
| Rychlý pøístup k pøedpøipraveným øešením. | Bezpeènostní zranitelnosti v neoficiálních image. |  
| Šetøí èas pøi vývoji. | Možnost zneužití škodlivého kódu. |  
| Podpora verzování a tagù. | Omezení rate limits u Docker Hubu. |  

---

### **5. Best Practices pro Windows**  

1. **Používejte oficiální image** (napø. od Microsoftu).  
2. **Kontrolujte tagy OS**:  
   - Verze image (napø. `ltsc2022`) musí odpovídat hostitelskému systému.  
3. **Skenujte image**:  
   - Nástroje jako `docker scan` nebo tøetí party (Trivy).  
4. **Automatizujte buildy**: Propojte Docker Hub/GHCR s GitHubem.  

---

### **6. Èasté problémy a øešení**  

- **Problém**: *"Image not found"* pøi použití veøejného repozitáøe.  
  - **Øešení**: Ovìøte název a tag image (napø. `mcr.microsoft.com/windows/servercore:ltsc2022`).  
- **Problém**: *"Rate limit exceeded"* na Docker Hubu.  
  - **Øešení**: Pøihlaste se (`docker login`) nebo upgrade úètu.  
- **Problém**: *"OS version mismatch"*.  
  - **Øešení**: Stáhnìte image se správným tagem OS (ovìøte `winver`).  

---

### **7. Pøíklady použití**  

#### **a) Spuštìní SQL Serveru z MCR**  

```powershell  
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Heslo123!" -p 1433:1433 mcr.microsoft.com/mssql/server:2022-latest  
```  

#### **b) Vlastní image na Docker Hubu** 

1. Sestavte image:  
   ```powershell  
   docker build -t mùj-úèet/moje-app:1.0 .  
   ```  
2. Nahrajte:  
   ```powershell  
   docker push mùj-úèet/moje-app:1.0  
   ```  

---

### **8. Alternativy k veøejným repozitáøùm**  

- **Privátní registry**: Azure Container Registry, GitLab Registry.  
- **Self-hostované registry**: Docker Registry (open-source), Harbor.  

---

### **9. Užiteèné zdroje** 

- **Odkazy**:  
  - [Docker Hub](https://hub.docker.com/)  
  - [Microsoft Container Registry](https://mcr.microsoft.com/)  
  - [Dokumentace Docker Hub](https://docs.docker.com/docker-hub/)  
- **Nástroje**:  
  - **Docker Desktop** (integrovaná správa repozitáøù).  
  - **Trivy** (skenování zranitelností).  

---

**Shrnutí**: Veøejné repozitáøe (Docker Hub, MCR, GHCR) jsou klíèové pro efektivní práci s Dockerem na Windows. Umožòují rychlý pøístup k oficiálním Windows image, ale vyžadují dodržování bezpeènostních pravidel a správné verzování. Pro kritické aplikace preferujte privátní registry nebo self-hostovaná øešení.
