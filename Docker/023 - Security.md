
# Security (Windows) 

---

### **1. Úvod**  

Bezpeènost v Dockeru na Windows vyaduje kombinaci správné konfigurace kontejnerù, izolace prostøedí a dodrování best practices. Windows kontejnery mají specifické vlastnosti (napø. integrace s Active Directory, Hyper-V izolace), které ovlivòují bezpeènostní strategii. Klíèové oblasti: izolace, správa pøístupù, skenování obrazù a ochrana hostitele.

---

### **2. Izolaèní reimy Windows kontejnerù** 

#### **a) Hyper-V Isolation**  

- **Princip**: Kadı kontejner bìí v lehké virtuální mašinì (VM) s vlastním jádrem.  
- **Vıhody**:  
  - Silnìjší izolace od hostitele a ostatních kontejnerù.  
  - Ochrana proti úniku prostøedkù nebo exploitùm jádra.  
- **Pouití**: Doporuèeno pro produkèní prostøedí.  
- **Pøíklad**:  
  ```powershell
  docker run --isolation=hyperv mcr.microsoft.com/windows/servercore:ltsc2022
  ```  

#### **b) Process Isolation**  

- **Princip**: Kontejnery sdílejí jádro hostitele (podobnì jako Linux kontejnery).  
- **Rizika**:  
  - Monost útoku pøes zranitelnosti jádra Windows.  
  - Ménì bezpeèné ne Hyper-V.  
- **Pouití**: Vhodné pro vıvoj a testování na stejné verzi OS.  

---

### **3. Zabezpeèení obrazù (Images)**  

- **Pouívejte dùvìryhodné základní obrazy**:  
  - Oficiální Microsoft obrazy (napø. `mcr.microsoft.com/windows/servercore`).  
  - Pravidelnì aktualizujte pomocí `docker pull`.  
- **Skenování zranitelností**:  
  ```powershell
  docker scan mcr.microsoft.com/windows/servercore:ltsc2022
  ```  
  - Nástroje: Docker Scout, Trivy, Azure Container Registry Vulnerability Scanning.  
- **Minimalizace velikosti obrazu**:  
  - Odstraòte nepotøebné balíèky a sluby (napø. PowerShell v produkci).  

---

### **4. Runtime Bezpeènost**  

#### **a) Omezení pøístupu**  

- **Nepouívejte kontejnery jako Administrator**:  
  ```powershell
  docker run --user "ContainerUser" moje_aplikace
  ```  
- **Zakázání privilegovanıch kontejnerù**:  
  Nepouívejte `--privileged` (na Windows není podporováno, ale relevantní pro Linux kontejnery na WSL2).  

#### **b) Omezení prostøedkù**  

- **Kvóty CPU a RAM**:  
  ```powershell
  docker run --cpus=2 --memory=4GB moje_aplikace
  ```  
- **Read-only filesystém**:  
  ```powershell
  docker run --read-only -v C:\Data:C:\App\Data:ro moje_aplikace
  ```  

#### **c) Sítì a porty**  

- **Minimalizujte otevøené porty**:  
  ```powershell
  docker run -p 80:80 --expose 80 web
  ```  
- **Pouívejte Docker sítì pro segmentaci**:  
  ```powershell
  docker network create interni_sit
  docker run --network=interni_sit database
  ```  

---

### **5. Zabezpeèení hostitele**  

- **Aktualizace**: Pravidelnì aktualizujte Windows Host a Docker Engine.  
- **Konfigurace Docker Daemonu**:  
  - Pouívejte TLS pro vzdálenı pøístup.  
  - Omezení pøístupu k Docker socketu (`docker.sock`).  
- **Windows Defender a Firewall**:  
  - Povolte kontrolu kontejnerù v Windows Defenderu.  
  - Nakonfigurujte pravidla firewallu pro Docker (porty 2375/2376).  

---

### **6. Správa tajemství (Secrets)**  

- **Docker Swarm Secrets**:  
  ```powershell
  echo "Heslo123!" | docker secret create db_password -
  docker service create --secret db_password moje_sluzba
  ```  
- **Pro Kubernetes na Windows**:  
  - Pouívejte Kubernetes Secrets nebo Azure Key Vault.  
- **Nikdy neukládejte tajemství v Dockerfile!**  

---

### **7. Monitorování a audit**  

- **Logování kontejnerù**:  
  ```powershell
  docker run --log-driver=json-file --log-opt max-size=10m moje_aplikace
  ```  
- **Nástroje**:  
  - Azure Monitor, Elastic Stack, Splunk.  
  - Detekce anomálií v síovém provozu (napø. Wireshark).  
- **Auditování**:  
  - Sledujte Docker události: `docker events`.  
  - Kontrolujte audit logy Windows (`Event Viewer`).  

---

### **8. Windows-specifická rizika**  

- **Útoky pøes PowerShell**:  
  - Zakázat nebo omezit PowerShell v produkèních kontejnerech.  
- **Active Directory Integration**:  
  - Kontejnery pøipojené k AD pouívejte pouze v dùvìryhodnıch sítích.  
- **Shared Kernel útoky**:  
  - U Process Isolation hrozí exploitace jádra Windows.  

---

### **9. Nástroje a Doporuèení**  

- **Docker Bench for Security**:  
  Automatizovaná kontrola konfigurace dle CIS benchmarkù.  
- **Azure Security Center**:  
  Detekuje hrozby v kontejnerech a obrazech.  
- **Best Practices**:  
  - Pouívejte Hyper-V isolation v produkci.  
  - Omezujte pøístup pomocí RBAC (Role-Based Access Control).  
  - Pravidelnì rotujte tajemství a certifikáty.  

---

### **10. Shrnutí**  

- **Izolace**: Hyper-V isolation je nutnost pro kritické aplikace.  
- **Obrazy**: Skenujte a aktualizujte, pouívejte minimální základ.  
- **Práva**: Nejniší moná oprávnìní pro kontejnery a uivatele.  
- **Sítì**: Segmentace a minimalizace otevøenıch portù.  
- **Windows specifika**: Pozor na integraci s AD, PowerShell a jádro.  

**Doporuèení**:  
- Pravidelnì testujte bezpeènost pomocí nástrojù jako Nessus nebo OpenSCAP.  
- Pro komplexní aplikace vyuívejte Kubernetes Security Policies (napø. Pod Security Policies).  

--- 

**Viz také**:  
- [Docker Security Best Practices](https://docs.docker.com/engine/security/)  
- [Microsoft Container Security](https://learn.microsoft.com/en-us/virtualization/windowscontainers/security/)
