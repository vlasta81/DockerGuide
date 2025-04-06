
# Docker Hub (Windows)

---

### **1. Co je Docker Hub?**  

- **Cloudový registr** pro ukládání, sdílení a správu Docker **imagí**.  
- Slouží jako centrální úložiště pro veřejné i soukromé kontejnerové obrazy.  
- **Funkce**:  
  - Hostování imagí (např. `nginx`, `microsoft/windows-server-core`).  
  - Automatizované buildy z GitHubu/Bitbucket.  
  - Týmová spolupráce a řízení přístupu.  
  - Bezpečnostní skenování (detekce zranitelností).  

---

### **2. Propojení Docker Hubu s Windows**  

- Docker Hub je **integrován do Docker Desktopu pro Windows**.  
- Umožňuje:  
  - Stahovat a používat veřejné **Windows-specifické image** (např. od Microsoftu).  
  - Nahrávat vlastní image vytvořené pro Windows.  
  - Spravovat privátní repozitáře pro firemní projekty.  

---

### **3. Klíčové funkce pro Windows uživatele**  

- **Oficiální Windows image**:  
  - Microsoft nabízí předpřipravené image (např. `mcr.microsoft.com/windows/servercore`, `mcr.microsoft.com/dotnet/framework/aspnet`).  
- **Podpora Windows Containers**:  
  - Image lze tagovat podle verze Windows (např. `ltsc2019`, `ltsc2022`).  
- **Integrace s Docker Desktop**:  
  - Přihlášení k Docker Hubu přímo z GUI.  

---

### **4. Registrace a nastavení**  

1. **Vytvořte účet** na [hub.docker.com](https://hub.docker.com/).  
2. **Přihlaste se v Docker Desktopu** nebo přes CLI:  
   ```powershell
   docker login
   ```  
3. Pro privátní repozitáře:  
   - Upgrade na **Docker Hub Pro/Team** (placené funkce).  

---

### **5. Základní příkazy pro práci s Docker Hubem**  

| Příkaz | Popis | Příklad |  
|--------|-------|---------|  
| `docker login` | Přihlášení k Docker Hubu | `docker login -u <uživatel>` |  
| `docker pull` | Stáhnout image | `docker pull mcr.microsoft.com/windows/servercore:ltsc2022` |  
| `docker push` | Nahrát image do registru | `docker push můj-účet/moje-image:v1` |  
| `docker search` | Hledat image | `docker search aspnet` |  
| `docker logout` | Odhlásit se | `docker logout` |  

---

### **6. Příklad: Použití image z Docker Hubu na Windows**  

1. Stáhněte Windows Server Core image:  
   ```powershell
   docker pull mcr.microsoft.com/windows/servercore:ltsc2022
   ```  
2. Spusťte kontejner:  
   ```powershell
   docker run -it mcr.microsoft.com/windows/servercore:ltsc2022 cmd
   ```  
3. Nahrání vlastní image:  
   - Označte image tagem s vaším Docker Hub účtem:  
     ```powershell
     docker tag moje-image:v1 můj-účet/moje-image:v1
     ```  
   - Nahrajte:  
     ```powershell
     docker push můj-účet/moje-image:v1
     ```  

---

### **7. Výhody a omezení**  

- **Výhody**:  
  - **Centralizované úložiště**: Snadný přístup k tisícům předpřipravených imagí.  
  - **Kompatibilita s Windows**: Oficiální image od Microsoftu.  
  - **Automatizace**: Propojení s GitHubem pro automatické buildy.  
- **Omezení**:  
  - **Rate limits**: Bezplatný účet má omezený počet stažení.  
  - **Bezpečnost**: Veřejné image mohou obsahovat zranitelnosti.  

---

### **8. Případy použití na Windows**  

- **Nasazení .NET aplikace**:  
  - Použijte image `mcr.microsoft.com/dotnet/framework/aspnet`.  
- **Testování na různých verzích Windows**:  
  - Stáhněte image pro Windows Server 2019/2022.  
- **CI/CD pipeline**:  
  - Nahrání finální image po sestavení do Docker Hubu pro nasazení.  

---

### **9. Užitečné tipy**  

- **Tagování**: Vždy specifikujte verzi OS v tagu (např. `:ltsc2022`).  
- **Automatizované buildy**: Propojte Docker Hub s GitHubem pro automatické aktualizace image.  
- **Privátní repozitáře**: Pro firemní projekty využívejte privátní úložiště.  

---

### **10. Alternativy k Docker Hubu**  

- **Azure Container Registry (ACR)**: Integrace s Azure ekosystémem.  
- **GitHub Container Registry**: Pro projekty hostované na GitHubu.  
- **Self-hostované registry**: Např. **Harbor** nebo **Nexus**.  

---

**Doporučení**: Pro práci s Windows kontejnery vždy ověřte kompatibilitu image s vaší verzí OS (např. `docker image inspect`).
