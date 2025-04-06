
# Logování (Windows)  

---

### **1. Úvod**  

Logování v Dockeru na Windows umožňuje sledovat výstupy a chyby kontejnerů, což je klíčové pro ladění a monitorování aplikací. Windows kontejnery a Linux kontejnery na WSL2 používají různé přístupy kvůli odlišným architekturám. Klíčové nástroje: **docker logs**, logging drivers a integrace s externími systémy (např. ELK Stack).

---

### **2. Logovací mechanizmy**  

#### **a) Logovací drivery (Logging Drivers)**  

- **Výchozí driver**: `json-file` (ukládá logy do JSON souborů na hostiteli).  
- **Podporované drivery na Windows**:  
  - `json-file` (default),  
  - `syslog` (odesílání logů do syslog serveru),  
  - `etwlogs` (Event Tracing for Windows – integrace s Windows Event Log),  
  - `gelf` (Graylog Extended Log Format),  
  - `awslogs` (Amazon CloudWatch),  
  - `none` (zakáže logování).  
- **Linux kontejnery na WSL2**: Podporují i `journald`, `fluentd` a další.  

#### **b) Formáty logů**  

- **JSON-file**: Strukturované logy s časovými značkami a metadaty.  
- **ETW**: Logy integrované do Windows Event Viewer (pouze pro Windows kontejnery).  

---

### **3. Konfigurace logování**  

#### **a) Globální nastavení (pro všechny kontejnery)**  

Upravte konfigurační soubor Docker daemonu (`daemon.json`):  
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```  
- **max-size**: Maximální velikost jednoho log souboru.  
- **max-file**: Počet rotovaných souborů.  

#### **b) Nastavení pro jednotlivé kontejnery**  

Použijte parametry `--log-driver` a `--log-opt` v PowerShellu:  
```powershell
docker run -d `
  --log-driver json-file `
  --log-opt max-size=5m `
  --log-opt max-file=2 `
  --name web nginx
```  

---

### **4. Kde se ukládají logy?**  

- **JSON-file driver**:  
  - Cesta: `C:\ProgramData\Docker\containers\<ID_kontejneru>\<ID_kontejneru>-json.log`.  
  - Přístup přes `docker inspect --format='{{.LogPath}}' <kontejner>`.  
- **ETW driver**:  
  - Logy jsou v **Event Viewer** pod cestou:  
    *Applications and Services Logs → Microsoft → Windows → Docker*.  

---

### **5. Práce s logy**  

#### **a) Základní příkazy**  

- Zobrazení logů kontejneru:  
  ```powershell
  docker logs web
  ```  
- Sledování logů v reálném čase:  
  ```powershell
  docker logs -f web
  ```  
- Export logů do souboru:  
  ```powershell
  docker logs web > C:\Logs\web.log
  ```  

#### **b) Pokročilé filtry**  

- Zobrazení posledních N řádků:  
  ```powershell
  docker logs --tail 100 web
  ```  
- Filtrace časového rozsahu:  
  ```powershell
  docker logs --since 2024-01-01 --until 2024-01-02 web
  ```  

---

### **6. Integrace s externími nástroji**  

#### **a) ELK Stack (Elasticsearch, Logstash, Kibana)**  

Příklad konfigurace pro `gelf` driver:  
```powershell
docker run -d `
  --log-driver gelf `
  --log-opt gelf-address=udp://localhost:12201 `
  --log-opt tag="web-app" `
  nginx
```  

#### **b) Azure Monitor**  

Použijte rozšíření **Azure Log Analytics**:  
```powershell
docker run -d `
  --log-driver=azuremonitor `
  --log-opt workspace-id=<ID> `
  --log-opt workspace-key=<KEY> `
  nginx
```  

---

### **7. Docker Compose a logování**  

Příklad `docker-compose.yml` pro Windows:  
```yaml
version: '3.8'
services:
  web:
    image: nginx
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
  db:
    image: mcr.microsoft.com/mssql/server:2019-latest
    logging:
      driver: etwlogs
```  

---

### **8. Řešení problémů** 

- **Logy se negenerují**:  
  - Ověřte, zda není použit driver `none`.  
  - Zkontrolujte oprávnění k zápisu do `C:\ProgramData\Docker\containers`.  
- **Přetečení disků**:  
  - Nastavte `max-size` a `max-file` pro rotaci logů.  
- **Chybějící logy v ETW**:  
  - Spusťte `docker run` s oprávněním správce.  

---

### **9. Best Practices**  

- **Produkční prostředí**:  
  - Používejte externí logging drivery (`syslog`, `gelf`).  
  - Vyhněte se ukládání logů na hostitelský disk.  
- **Vývoj**:  
  - Pro rychlý přístup použijte `json-file` s rotací.  
- **Citlivá data**:  
  - Nikdy nelogujte hesla nebo tokeny.  
  - Používejte filtry pro maskování citlivých informací.  

---

### **10. Shrnutí**  

- **Logovací drivery**: Volba závisí na prostředí (Windows vs. Linux kontejnery).  
- **Rotace logů**: Nastavte `max-size` a `max-file` pro prevenci přetečení.  
- **Integrace**: Pro centralizaci logů použijte ELK, Azure Monitor nebo Splunk.  
- **Windows specifika**: ETW pro integraci s Event Viewer, pozor na cesty v `C:\ProgramData\Docker`.  

**Doporučení**:  
- Pravidelně auditujte logy kvůli bezpečnostním hrozbám.  
- Pro komplexní aplikace použijte Docker Compose pro jednotnou konfiguraci.  

--- 

**Viz také**:  
- [Docker Logging Documentation](https://docs.docker.com/config/containers/logging/)  
- [Windows Container Logging](https://learn.microsoft.com/en-us/virtualization/windowscontainers/management/logging)
