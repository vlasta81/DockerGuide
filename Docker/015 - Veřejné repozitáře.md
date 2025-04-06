
# Ve�ejn� repozit��e (Windows)

---

### **1. Co jsou ve�ejn� repozit��e?**  

- **Cloudov� �lo�i�t�** pro sd�len� a distribuci Docker imag�.  
- **Vyu�it�**:  
  - Sta�en� hotov�ch imag� (nap�. datab�ze, webov� servery).  
  - Sd�len� vlastn�ch imag� s komunitou.  
- **Kl��ov� vlastnosti**:  
  - Ve�ejn� p��stup (bez nutnosti autentizace).  
  - Podpora tagov�n�, verzov�n� a automatick�ch build�.  

---

### **2. Hlavn� ve�ejn� repozit��e pro Windows**  

#### **a) Docker Hub**  

- **V�choz� registry** pro Docker komunitu.  
- **Ofici�ln� Windows image**:  
  - Od Microsoftu (nap�. `mcr.microsoft.com/windows/servercore`, `mcr.microsoft.com/dotnet/framework`).  
  - Vy�aduj� spr�vn� **tag verze OS** (nap�. `ltsc2022`).  
- **Omezen�**:  
  - Bezplatn� ��ty maj� **rate limits** (100 sta�en�/6 hodin pro anonymn� u�ivatele).  

#### **b) Microsoft Container Registry (MCR)**  

- Ofici�ln� registry Microsoftu pro Windows a .NET image.  
- **Adresa**: `mcr.microsoft.com`.  
- **P��klady**:  
  - `mcr.microsoft.com/windows/servercore:ltsc2022`  
  - `mcr.microsoft.com/dotnet/aspnet:6.0`  

#### **c) GitHub Container Registry (GHCR)**  

- Ve�ejn� repozit��e hostovan� na GitHubu.  
- **V�hody**: Integrace s GitHub Actions, automatick� buildy z k�du.  

---

### **3. Pr�ce s ve�ejn�mi repozit��i na Windows**  

#### **a) Sta�en� image (Pull)**  

```powershell  
docker pull mcr.microsoft.com/windows/servercore:ltsc2022  
docker pull nginx  # Implicitn� z Docker Hubu  
```  

#### **b) Nahr�n� vlastn� image (Push)**  

1. Ozna�te image tagem podle ve�ejn�ho repozit��e:  
   ```powershell  
   docker tag m�j-image:verce u�ivatel/m�j-image:verze  
   ```  
2. Nahrajte do Docker Hubu:  
   ```powershell  
   docker push u�ivatel/m�j-image:verze  
   ```  

#### **c) Hled�n� imag�**  

- **P��mo v CLI**:  
  ```powershell  
  docker search microsoft/dotnet  
  ```  
- **Webov� rozhran�**: [Docker Hub](https://hub.docker.com/), [MCR](https://mcr.microsoft.com/).  

---

### **4. V�hody a rizika ve�ejn�ch repozit���**  

| **V�hody** | **Rizika** |  
|------------|------------|  
| Rychl� p��stup k p�edp�ipraven�m �e�en�m. | Bezpe�nostn� zranitelnosti v neofici�ln�ch image. |  
| �et�� �as p�i v�voji. | Mo�nost zneu�it� �kodliv�ho k�du. |  
| Podpora verzov�n� a tag�. | Omezen� rate limits u Docker Hubu. |  

---

### **5. Best Practices pro Windows**  

1. **Pou��vejte ofici�ln� image** (nap�. od Microsoftu).  
2. **Kontrolujte tagy OS**:  
   - Verze image (nap�. `ltsc2022`) mus� odpov�dat hostitelsk�mu syst�mu.  
3. **Skenujte image**:  
   - N�stroje jako `docker scan` nebo t�et� party (Trivy).  
4. **Automatizujte buildy**: Propojte Docker Hub/GHCR s GitHubem.  

---

### **6. �ast� probl�my a �e�en�**  

- **Probl�m**: *"Image not found"* p�i pou�it� ve�ejn�ho repozit��e.  
  - **�e�en�**: Ov��te n�zev a tag image (nap�. `mcr.microsoft.com/windows/servercore:ltsc2022`).  
- **Probl�m**: *"Rate limit exceeded"* na Docker Hubu.  
  - **�e�en�**: P�ihlaste se (`docker login`) nebo upgrade ��tu.  
- **Probl�m**: *"OS version mismatch"*.  
  - **�e�en�**: St�hn�te image se spr�vn�m tagem OS (ov��te `winver`).  

---

### **7. P��klady pou�it�**  

#### **a) Spu�t�n� SQL Serveru z MCR**  

```powershell  
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Heslo123!" -p 1433:1433 mcr.microsoft.com/mssql/server:2022-latest  
```  

#### **b) Vlastn� image na Docker Hubu** 

1. Sestavte image:  
   ```powershell  
   docker build -t m�j-��et/moje-app:1.0 .  
   ```  
2. Nahrajte:  
   ```powershell  
   docker push m�j-��et/moje-app:1.0  
   ```  

---

### **8. Alternativy k ve�ejn�m repozit���m**  

- **Priv�tn� registry**: Azure Container Registry, GitLab Registry.  
- **Self-hostovan� registry**: Docker Registry (open-source), Harbor.  

---

### **9. U�ite�n� zdroje** 

- **Odkazy**:  
  - [Docker Hub](https://hub.docker.com/)  
  - [Microsoft Container Registry](https://mcr.microsoft.com/)  
  - [Dokumentace Docker Hub](https://docs.docker.com/docker-hub/)  
- **N�stroje**:  
  - **Docker Desktop** (integrovan� spr�va repozit���).  
  - **Trivy** (skenov�n� zranitelnost�).  

---

**Shrnut�**: Ve�ejn� repozit��e (Docker Hub, MCR, GHCR) jsou kl��ov� pro efektivn� pr�ci s Dockerem na Windows. Umo��uj� rychl� p��stup k ofici�ln�m Windows image, ale vy�aduj� dodr�ov�n� bezpe�nostn�ch pravidel a spr�vn� verzov�n�. Pro kritick� aplikace preferujte priv�tn� registry nebo self-hostovan� �e�en�.
