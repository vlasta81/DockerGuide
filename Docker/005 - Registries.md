
# Docker Registries (Windows)

---

### **1. Co je Docker Registry?** 

- **Centralizované úložiště** pro ukládání, sdílení a správu Docker **imagí**.  
- Umožňuje **stahovat** (pull) a **nahrávat** (push) image pro individuální i týmovou práci.  
- **Typy registrů**:  
  - **Veřejné registry** (např. Docker Hub, Microsoft Container Registry).  
  - **Privátní registry** (např. Azure Container Registry, GitHub Container Registry, self-hostované registry).  

---

### **2. Klíčové registry pro Windows**  

#### **a) Docker Hub**  

- **Výchozí veřejný registry** pro Docker komunitu.  
- Obsahuje **oficiální Windows image** od Microsoftu (např. `mcr.microsoft.com/windows/servercore`).  
- **Omezení**: Bezplatný účet má limit 100 stažení/6 hodin (pro anonymní uživatele).  

#### **b) Microsoft Container Registry (MCR)**  

- Oficiální registry Microsoftu pro **Windows-specifické image**.  
- Adresa: `mcr.microsoft.com`.  
- Příklady:  
  - `mcr.microsoft.com/windows/servercore:ltsc2022`  
  - `mcr.microsoft.com/dotnet/framework/aspnet:4.8`  

#### **c) Azure Container Registry (ACR)** 

- **Privátní registry** integrovaný do Azure ekosystému.  
- Podpora **Windows i Linux imagí**, automatické buildy, georeplikace.  
- **Cíl**: Bezpečné hostování firemních imagí.  

#### **d) GitHub Container Registry (GHCR)**  

- Registry přímo v GitHubu.  
- **Výhody**: Automatické buildy z GitHub Actions, integrace s kódem.  

---

### **3. Práce s registry na Windows**  

#### **a) Přihlášení k Docker Hubu**  

```powershell  
docker login  # Pro Docker Hub  
docker login mcr.microsoft.com  # Pro MCR  
docker login ghcr.io  # Pro GitHub Registry  
```  

#### **b) Stahování imagí**  

```powershell  
docker pull mcr.microsoft.com/windows/servercore:ltsc2022  
docker pull můj-účet/moje-image:tag  # Z privátního registru  
```  

#### **c) Nahrávání imagí**  

1. Označte image tagem s cestou k registru:  
   ```powershell  
   docker tag moje-image:v1 ghcr.io/můj-účet/moje-image:v1  
   ```  
2. Nahrajte:  
   ```powershell  
   docker push ghcr.io/můj-účet/moje-image:v1  
   ```  

---

### **4. Nastavení privátního registru na Windows**  

#### **a) Azure Container Registry**

1. Vytvořte ACR v Azure Portálu.  
2. Přihlaste se:  
   ```powershell  
   docker login <název-registru>.azurecr.io  
   ```  
3. Nahrávejte image s tagem ve formátu: `<název-registru>.azurecr.io/moje-image:tag`.  

#### **b) Self-hostovaný registry (Docker Registry)**  

1. Spusťte lokální registry jako kontejner:  
   ```powershell  
   docker run -d -p 5000:5000 --name registry registry:2  
   ```  
2. Nahrávejte image s tagem `localhost:5000/moje-image:tag`.  

---

### **5. Výhody a omezení**  

| **Výhody** | **Omezení** |  
|------------|-------------|  
| Centralizovaná správa imagí. | Bezplatné registry mají limity (Docker Hub). |  
| Bezpečnostní skenování (ACR, GHCR). | Privátní registry vyžadují konfiguraci a náklady. |  
| Podpora Windows-specifických imagí. | Nutnost kompatibility verzí OS (Windows image). |  

---

### **6. Časté problémy**  

- **Chyba přístupu k privátnímu registru**:  
  - Řešení: Ověřte přihlášení (`docker login`) a oprávnění účtu.  
- **Nekompatibilní tagy OS**:  
  - Chyba: *"The container OS does not match the host OS"*.  
  - Řešení: Stáhněte image s tagem odpovídajícím verzi Windows hostitele.  

---

### **7. Best Practices**  

- **Tagování**: Vždy uvádějte verzi OS a aplikace (např. `:ltsc2022-v1`).  
- **Automatizace**: Propojte registry s CI/CD nástroji (GitHub Actions, Azure Pipelines).  
- **Zálohování**: Exportujte image pomocí `docker save` pro offline použití.  
- **Bezpečnost**: Pravidelně aktualizujte base image a skenujte je na zranitelnosti.  

---

### **8. Příklady použití**  

#### **Nasazení ASP.NET aplikace přes ACR**  

1. Sestavte image s tagem:  
   ```powershell  
   docker build -t můj-acr.azurecr.io/moje-webovka:1.0 .  
   ```  
2. Nahrajte do ACR:  
   ```powershell  
   docker push můj-acr.azurecr.io/moje-webovka:1.0  
   ```  
3. Spusťte z ACR:  
   ```powershell  
   docker run -p 8080:80 můj-acr.azurecr.io/moje-webovka:1.0  
   ```  

---

### **9. Užitečné zdroje**  

- **Dokumentace**:  
  - [Azure Container Registry](https://learn.microsoft.com/cs-cz/azure/container-registry/)  
  - [Docker Hub](https://docs.docker.com/docker-hub/)  
- **Nástroje**:  
  - **Docker Desktop** (správa registrů přes GUI).  
  - **Trivy** (skenování zranitelností imagí).  

---

**Tip**: Pro práci s Windows image vždy ověřte, že používáte **oficiální zdroje** (např. `mcr.microsoft.com`), abyste předešli bezpečnostním rizikům.
