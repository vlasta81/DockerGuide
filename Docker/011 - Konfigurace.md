
# Konfigurace a konfigurace daemonu (Windows)

---

### **1. Úvod do Docker Daemonu na Windows**  

- **Docker Daemon** (`dockerd`): Hlavní služba spravující kontejnery, image, sítě a svazky.  
- **Funkce**:  
  - Komunikuje s Docker CLI a přijímá příkazy (např. `docker run`).  
  - Spravuje kontejnery a jejich životní cyklus.  
- **Režimy na Windows**:  
  - **Windows Containers**: Přímá integrace s Windows kernelem.  
  - **Linux Containers**: Běh přes WSL 2 nebo Hyper-V.  

---

### **2. Konfigurační soubor `daemon.json`**  

- **Účel**: Definice nastavení Docker Daemonu (registry, logování, síťování, úložiště).  
- **Umístění na Windows**:  
  ```  
  %programdata%\docker\config\daemon.json  
  ```  
- **Příklad konfigurace**:  
  ```json  
  {  
    "debug": true,  
    "registry-mirrors": ["https://registry.example.com"],  
    "insecure-registries": ["localhost:5000"],  
    "log-driver": "json-file",  
    "log-opts": { "max-size": "10m" },  
    "storage-driver": "windowsfilter"  
  }  
  ```  

---

### **3. Klíčové konfigurační parametry**  

| **Parametr**          | **Popis**                                  |  
|-----------------------|--------------------------------------------|  
| `registry-mirrors`    | Zrcadla veřejných registrů (např. Docker Hub). |  
| `insecure-registries` | Registry bez HTTPS (např. lokální registry). |  
| `log-driver`          | Formát logů (`json-file`, `syslog`).       |  
| `storage-driver`      | Ovladač úložiště (`windowsfilter` pro Windows kontejnery). |  
| `dns`                 | Vlastní DNS servery pro kontejnery.        |  
| `experimental`        | Povolení experimentálních funkcí.          |  

---

### **4. Konfigurace přes Docker Desktop**  

Docker Desktop pro Windows umožňuje nastavení daemonu přes GUI:  
1. **Settings → Docker Engine**:  
   - Úprava `daemon.json` přímo v editoru.  
2. **Settings → Resources**:  
   - Přidělení CPU, RAM, a disk space pro Docker.  
   - Nastavení sdílených složek (**File Sharing**).  
3. **Settings → WSL Integration**:  
   - Integrace s WSL 2 pro Linux kontejnery.  

---

### **5. Windows-specifická nastavení**  

#### **a) Síťové ovladače**  

- **NAT**: Výchozí pro Windows kontejnery.  
- **Transparentní**: Přímé připojení k síti hostitele (vyžaduje Windows Server).  
- **L2 Bridge**: Pro pokročilé síťové scénáře.  

#### **b) Hyper-V vs. WSL 2**  

- **Hyper-V**:  
  - Starší režim pro Linux kontejnery.  
  - Vyšší režie, ale lepší kompatibilita.  
- **WSL 2**:  
  - Doporučeno pro moderní Windows 10/11.  
  - Nižší režie, rychlejší start kontejnerů.  

---

### **6. Bezpečnostní konfigurace**  

- **TLS autentizace**: Zabezpečení komunikace s daemonem.  
  - Vyžaduje certifikáty pro klienta a server.  
- **Omezení přístupu k daemonu**:  
  - Nastavení v `daemon.json`:  
    ```json  
    {  
      "tlsverify": true,  
      "tlscacert": "C:\\certs\\ca.pem",  
      "tlscert": "C:\\certs\\server-cert.pem",  
      "tlskey": "C:\\certs\\server-key.pem"  
    }  
    ```  

---

### **7. Užitečné příkazy pro správu**  

| **Příkaz**                     | **Popis**                              |  
|--------------------------------|----------------------------------------|  
| `Restart-Service docker`       | Restartuje Docker Daemon (PowerShell). |  
| `docker system info`           | Zobrazí konfiguraci Dockeru.           |  
| `docker stats`                 | Monitoruje využití zdrojů.             |  
| `docker logs <ID>`             | Zobrazí logy kontejneru.               |  

---

### **8. Časté problémy a řešení**  

- **Problém**: Změny v `daemon.json` se neprojeví.  
  - **Řešení**: Restartujte Docker Desktop nebo službu Docker.  
- **Problém**: Kontejnery nemají přístup k internetu.  
  - **Řešení**: Ověřte síťový režim a firewall.  
- **Problém**: Chyba úložiště (`windowsfilter`).  
  - **Řešení**: Smažte nefunkční image a kontejnery (`docker system prune`).  

---

### **9. Best Practices**  

1. **Zálohujte `daemon.json`**: Před změnami vytvořte kopii.  
2. **Testujte v dev prostředí**: Neprovádějte změny přímo v produkci.  
3. **Minimalizujte experimentální funkce**: Pokud nejsou nutné.  
4. **Aktualizujte Docker Desktop**: Pro nejnovější bezpečnostní záplaty.  

---

### **10. Užitečné zdroje**  

- **Dokumentace**:  
  - [Docker – Konfigurace daemonu](https://docs.docker.com/engine/reference/commandline/dockerd/)  
  - [Microsoft – Windows Container Configuration](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/manage-docker/configure-docker-daemon)  
- **Nástroje**:  
  - **Docker Desktop** (GUI pro snadnou konfiguraci).  
  - **WSL 2** (pro optimalizaci výkonu).  

---

**Shrnutí**: Konfigurace Docker Daemonu na Windows zahrnuje úpravu `daemon.json`, správu zdrojů přes Docker Desktop a volbu síťového režimu. Klíčové je testování změn a respektování Windows-specifických omezení (např. kompatibilita verzí OS).
