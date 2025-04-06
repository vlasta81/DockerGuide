
# Docker Containers (Windows)

---

### **1. Co je Docker Container?**  

- **Kontejner** = Izolované prostředí pro běh aplikace se všemi závislostmi (kód, runtime, knihovny, konfigurace).  
- **Vytváří se z image** (předpřipravené šablony).  
- **Výhody**:  
  - Konzistence napříč prostředími (vývoj → produkce).  
  - Rychlé spouštění a zastavování.  
  - Efektivní využití systémových prostředků (oproti VM).  

---

### **2. Windows Containers vs. Linux Containers**  

- **Windows Containers**:  
  - Běží přímo na Windows kernelu.  
  - Vyžadují shodu **verze OS** hostitele a image (např. `ltsc2019`, `ltsc2022`).  
  - Vhodné pro aplikace závislé na Windows (např. .NET Framework, IIS).  
- **Linux Containers**:  
  - Běží přes **WSL 2** nebo **Hyper-V**.  
  - Menší velikost, širší podpora open-source nástrojů.  

---

### **3. Požadavky na Windows**  

- **Podporované edice OS**:  
  - Windows 10/11 Pro, Enterprise, Education (64-bit).  
  - Pro Windows Home: Nutné povolit **WSL 2**.  
- **Virtualizace**: Musí být aktivována v BIOS/UEFI.  
- **Doporučený hardware**: 4 GB RAM, SSD, 64-bit procesor.  

---

### **4. Práce s kontejnery na Windows**  

#### **Klíčové příkazy**  

| Příkaz | Popis | Příklad |  
|--------|-------|---------|  
| `docker run <image>` | Spustí kontejner z image | `docker run mcr.microsoft.com/windows/servercore:ltsc2022` |  
| `docker ps` | Zobrazí běžící kontejnery | `docker ps -a` (všechny kontejnery) |  
| `docker stop <ID>` | Zastaví kontejner | `docker stop 1a2b3c` |  
| `docker rm <ID>` | Smaže kontejner | `docker rm 1a2b3c` |  
| `docker exec -it <ID> <příkaz>` | Spustí příkaz v běžícím kontejneru | `docker exec -it 1a2b3c cmd` |  
| `docker logs <ID>` | Zobrazí logy kontejneru | `docker logs 1a2b3c` |  

---

### **5. Životní cyklus kontejneru**  

1. **Vytvoření**: `docker run` (stáhne image, pokud není lokálně).  
2. **Běh**: Kontejner je aktivní a zpracovává úlohy.  
3. **Zastavení**: `docker stop` (ukončení procesů).  
4. **Smazání**: `docker rm` (odstraní kontejner z disku).  

---

### **6. Síťování a svazky (volumes)**  

- **Síťové režimy**:  
  - `-p <host_port>:<container_port>`: Mapování portů (např. `-p 80:80`).  
  - `--network`: Vlastní síťové nastavení.  
- **Svazky**:  
  - Pro trvalá data: `-v <host_cesta>:<container_cesta>`.  
  - Příklad:  
    ```powershell  
    docker run -v C:\Data:D:\Data mcr.microsoft.com/windows/servercore  
    ```  

---

### **7. Časté problémy a řešení**  

- **Chyba verze OS**:  
  - *"The container OS does not match the host OS"* → Stáhněte image se správným tagem (např. `ltsc2022`).  
- **Konflikt portů**:  
  - Změňte hostitelský port (např. `-p 8080:80`).  
- **Práva k souborům ve svazcích**:  
  - Nastavte sdílení složky v Docker Desktopu (**Settings → Resources → File Sharing**).  

---

### **8. Příklady použití**  

#### **Spuštění webového serveru IIS**  

1. Stáhněte image:  
   ```powershell  
   docker pull mcr.microsoft.com/windows/servercore/iis  
   ```  
2. Spusťte kontejner:  
   ```powershell  
   docker run -d -p 80:80 --name můj-iis mcr.microsoft.com/windows/servercore/iis  
   ```  
3. Otevřete `http://localhost` v prohlížeči.  

#### **Interaktivní kontejner s PowerShell** 

```powershell  
docker run -it mcr.microsoft.com/powershell pwsh  
```  

---

### **9. Best Practices**  

- **Minimalizujte počet kontejnerů**: Používejte jeden kontejner na jednu službu.  
- **Orchestrace**: Pro komplexní aplikace využijte **Docker Compose** nebo **Kubernetes**.  
- **Aktualizace**: Pravidelně stahujte nové verze image kvůli bezpečnostním záplatám.  
- **Logování**: Ukládejte logy do externích služeb (např. Elasticsearch, Splunk).  

---

### **10. Výhody a omezení**  

- **Výhody**:  
  - **Izolace**: Aplikace neovlivňují hostitelský systém.  
  - **Škálovatelnost**: Snadné spouštění více instancí.  
  - **Portabilita**: Kontejnery lze spustit kdekoli (cloud, lokální PC).  
- **Omezení**:  
  - **Výkon**: Windows kontejnery jsou větší a pomalejší než Linuxové.  
  - **Kompatibilita**: Omezená podpora starších verzí Windows.  

---

### **11. Užitečné zdroje**  

- **Dokumentace**:  
  - [Microsoft – Windows Containers](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/)  
  - [Docker – Get Started](https://docs.docker.com/get-started/)  
- **Komunita**: Stack Overflow, Docker Forums.  
- **Nástroje**: Docker Desktop, Visual Studio (integrovaná podpora Dockeru).  

---

**Tip**: Pro lepší výkon s Linux kontejnery na Windows aktivujte **WSL 2** v Docker Desktopu (Nastavení → General → Use WSL 2 based engine).
