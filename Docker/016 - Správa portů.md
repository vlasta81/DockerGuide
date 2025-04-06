
# Správa portů (Windows)

---

### **1. Základy správy portů v Dockeru**  

- **Porty** umožňují komunikaci mezi kontejnerem a hostitelem/externí sítí.  
- **Port mapping**: Mapování portu **hostitele** na port **kontejneru**.  
- **Výchozí chování**: Bez mapování není služba v kontejneru zvenčí dostupná.  

---

### **2. Klíčové příkazy pro správu portů**  

| **Příkaz** | **Popis** | **Příklad** |  
|------------|-----------|-------------|  
| `docker run -p <host_port>:<container_port>` | Mapuje port hostitele na port kontejneru. | `docker run -p 8080:80 nginx` |  
| `docker run -P` | Automaticky mapuje všechny **exponované porty** na náhodné porty hostitele. | `docker run -P httpd` |  
| `docker port <ID>` | Zobrazí mapované porty pro daný kontejner. | `docker port můj-kontejner` |  
| `docker ps` | Zobrazí běžící kontejnery a jejich porty. | – |  

---

### **3. Typy mapování portů na Windows** 

#### **a) Explicitní mapování**  

- **Příklad**: `-p 80:80` → Port 80 hostitele směřuje na port 80 kontejneru.  
- **Specifikace protokolu** (TCP/UDP):  
  ```powershell  
  docker run -p 53:53/udp dns-server  
  ```  

#### **b) Dynamické mapování**  

- Docker přiřadí náhodný port hostitele:  
  ```powershell  
  docker run -p 80 # Hostitel např. přiřadí 32768  
  ```  

#### **c) Exponované porty v Dockerfile** 

- Instrukce `EXPOSE` v Dockerfile označuje porty, které kontejner **poslouchá**:  
  ```dockerfile  
  EXPOSE 80/tcp  
  ```  
  - Nezajišťuje mapování – musí se použít `-p` nebo `-P`.  

---

### **4. Síťové režimy na Windows**  

- **NAT (výchozí)**:  
  - Kontejnery sdíjí IP hostitele.  
  - Porty se mapují explicitně.  
- **Transparentní režim**:  
  - Kontejnery dostanou vlastní IP v síti hostitele.  
  - Vyžaduje Windows Server.  
- **Overlay síť**:  
  - Pro komunikaci mezi kontejnery na různých hostitelích (v Docker Swarm).  

---

### **5. Časté problémy a řešení**  

| **Problém** | **Příčina** | **Řešení** |  
|-------------|-------------|-------------|  
| *"Port is already allocated"* | Port hostitele je obsazen jinou službou. | Změňte hostitelský port (např. `-p 8080:80`). |  
| *"No connection"* | Chyba v mapování nebo firewall. | Ověřte mapování (`docker port`) a vypněte firewall. |  
| *"Exposed port not accessible"* | Chybí `-p` nebo `-P`. | Přidejte mapování portů při spuštění. |  

---

### **6. Best Practices**  

1. **Minimalizujte otevřené porty**: Zveřejňujte pouze nezbytné porty.  
2. **Používejte explicitní mapování**: Pro lepší kontrolu (např. `-p 80:80`).  
3. **Testujte lokálně**: Ověřte funkčnost před nasazením do produkce.  
4. **Logování**: Sledujte přístupy k portům pomocí `docker logs`.  

---

### **7. Příklady použití**  

#### **a) Webový server IIS**  

```powershell  
docker run -d -p 8080:80 --name můj-web mcr.microsoft.com/windows/servercore/iis  
```  
- Aplikace bude dostupná na `http://localhost:8080`.  

#### **b) SQL Server**  

```powershell  
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Heslo123!" -p 1433:1433 mcr.microsoft.com/mssql/server:2022-latest  
```  

#### **c) Více portů najednou**  

```powershell  
docker run -p 80:80 -p 443:443 --name můj-server webserver-image  
```  

---

### **8. Nástroje pro správu portů**  

- **Docker Desktop Dashboard**: Přehledné zobrazení mapovaných portů v GUI.  
- **PowerShell**: Příkazy jako `netstat -ano` pro kontrolu obsazených portů.  
- **Wireshark**: Pokročilá analýza síťového provozu.  

---

### **9. Užitečné zdroje**  

- **Dokumentace**:  
  - [Docker – Network settings](https://docs.docker.com/engine/reference/commandline/run/#publish-or-expose-port--p---expose)  
  - [Microsoft – Windows Container Networking](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/container-networking/architecture)  
- **Komunita**: Fóra Stack Overflow, Docker Forums.  

---

**Shrnutí**: Správa portů v Dockeru na Windows zahrnuje mapování portů hostitele a kontejneru pomocí `-p`/`-P`, volbu síťového režimu a řešení konfliktů. Klíčové je dodržovat bezpečnostní pravidla a testovat konfiguraci před nasazením. Pro složitější scénáře využijte Docker Compose nebo orchestrátory jako Kubernetes.
