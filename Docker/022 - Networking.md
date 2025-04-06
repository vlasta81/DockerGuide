
# Networking (Windows) 

---

### **1. Úvod** 

Docker Networking umožňuje kontejnerům komunikovat mezi sebou, s hostitelským systémem i externími sítěmi. Na Windows má síťová konfigurace specifické vlastnosti kvůli rozdílům mezi **Windows kontejnery** a **Linux kontejnery na WSL2**. Znalost síťových režimů a driverů je klíčová pro návrh škálovatelných aplikací.

---

### **2. Síťové režimy a drivery na Windows**  

#### **a) Podporované síťové drivery**  

1. **NAT (výchozí pro Windows kontejnery)**:  
   - Kontejnery sdílejí virtuální síť s hostitelem přes NAT (Network Address Translation).  
   - Ideální pro izolované prostředí (např. vývoj).  
   - **Omezení**: Nelze přímo připojit kontejnery z externí sítě.  

2. **Transparentní síť (Transparent)**:  
   - Kontejnery získají IP adresu z fyzické sítě (jako samostatná zařízení).  
   - Vyžaduje **Windows Server s Host Network Service (HNS)**.  

3. **Overlay síť**:  
   - Pro komunikaci mezi kontejnery v Docker Swarm clusteru.  
   - Podporuje multi-host sítě (např. pro cloudové aplikace).  

4. **L2Bridge/L2Tunnel**:  
   - Pokročilé režimy pro integraci s externí infrastrukturou (např. Azure Virtual Network).  

5. **WSL2 back-end (pro Linux kontejnery)**:  
   - Linux kontejnery na Windows používají virtuální síť WSL2 s vlastním DHCP.  

#### **b) Porovnání režimů**  

| **Režim**       | **Použití**                     | **Podpora Windows kontejnerů** |  
|------------------|----------------------------------|--------------------------------|  
| NAT              | Izolovaný vývoj, testování      | ✔️                             |  
| Transparentní    | Produkce (přímé připojení)      | ✔️ (Windows Server)            |  
| Overlay          | Docker Swarm                    | ✔️                             |  
| Host             | Maximální výkon (sdílení portů) | ❌ (Jen Linux na WSL2)         |  

---

### **3. Praktická konfigurace (PowerShell)**  

#### **a) Vytvoření vlastní sítě**  

```powershell
# Vytvoření NAT sítě
docker network create --driver nat moje_nat_sit

# Vytvoření transparentní sítě
docker network create --driver transparent moje_transparent_sit
```  

#### **b) Připojení kontejneru k síti**  

```powershell
docker run -d --name web --network moje_nat_sit nginx
```  

#### **c) Propojení více kontejnerů**  

```powershell
# Síť "app_network" pro komunikaci mezi backendem a DB
docker network create app_network
docker run -d --name database --network app_network mcr.microsoft.com/mssql/server:2019-latest
docker run -d --name backend --network app_network moje_aplikace
```  

#### **d) Port mapping**  

```powershell
# Mapování portu 80 z kontejneru na hostitele
docker run -d --name web -p 80:80 nginx
```  

---

### **4. Specifika pro Windows**  

- **Windows kontejnery vs. Linux kontejnery**:  
  - Windows kontejnery podporují **NAT** a **transparentní** režim.  
  - Linux kontejnery na WSL2 používají **WSL2 virtuální síť** (automaticky konfigurovanou).  
- **Hyper-V Isolation**:  
  - Umožňuje lepší izolaci, ale může omezit přístup k hostitelským portům.  
- **Firewall**:  
  - Pravidla firewallu na Windows mohou blokovat komunikaci mezi kontejnery.  
  - Řešení: Povolte **DockerExe** a **DockerDesktop** v nastavení firewallu.  

---

### **5. Docker Compose a sítě**  

Příklad `docker-compose.yml` pro Windows:  
```yaml
version: '3.8'
services:
  web:
    image: nginx
    ports:
      - "80:80"
    networks:
      - app_net
  db:
    image: mcr.microsoft.com/mssql/server:2019-latest
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=Heslo123!
    networks:
      - app_net
networks:
  app_net:
    driver: nat
```  

---

### **6. Řešení problémů**  

- **Kontejnery se nevidí v síti**:  
  - Ověřte, zda jsou kontejnery ve stejné síti (`docker network inspect <síť>`).  
  - Zkontrolujte firewall a Hyper-V nastavení.  
- **Port je již obsazen**:  
  - Najděte proces blokující port: `netstat -ano | findstr :80`.  
- **DNS selhává**:  
  - Použijte interní DNS Dockeru (kontejnery komunikují přes jména služeb).  

---

### **7. Pokročilá témata**  

- **Připojení k Azure Virtual Network**:  
  Použijte **L2Bridge** nebo **Azure CNI plugin** pro hybridní cloudové scénáře.  
- **Síťové politiky (Calico)**:  
  Definujte pravidla pro povolenou komunikaci mezi kontejnery (např. v Kubernetes).  

---

### **8. Shrnutí**  

- **NAT síť**: Výchozí volba pro izolovaný vývoj na Windows.  
- **Transparentní síť**: Produkční nasazení s přímým připojením k fyzické síti.  
- **Overlay síť**: Pro Docker Swarm a distribuované aplikace.  
- **Windows vs. Linux kontejnery**: Zásadní rozdíly v síťových režimech a integraci s WSL2.  

**Doporučení**:  
- Při vývoji používejte **Docker Compose** pro automatickou správu sítí.  
- Pro produkci na Windows Serveru preferujte **transparentní síť** nebo **L2Bridge**.  
- Pravidelně aktualizujte Docker Desktop pro podporu nových síťových funkcí.  

--- 

**Viz také**:  
- [Docker Networking Overview](https://docs.docker.com/network/)  
- [Windows Container Networking](https://learn.microsoft.com/en-us/virtualization/windowscontainers/container-networking/architecture)
