
# Security (Windows) 

---

### **1. �vod**  

Bezpe�nost v Dockeru na Windows vy�aduje kombinaci spr�vn� konfigurace kontejner�, izolace prost�ed� a dodr�ov�n� best practices. Windows kontejnery maj� specifick� vlastnosti (nap�. integrace s Active Directory, Hyper-V izolace), kter� ovliv�uj� bezpe�nostn� strategii. Kl��ov� oblasti: izolace, spr�va p��stup�, skenov�n� obraz� a ochrana hostitele.

---

### **2. Izola�n� re�imy Windows kontejner�** 

#### **a) Hyper-V Isolation**  

- **Princip**: Ka�d� kontejner b�� v lehk� virtu�ln� ma�in� (VM) s vlastn�m j�drem.  
- **V�hody**:  
  - Siln�j�� izolace od hostitele a ostatn�ch kontejner�.  
  - Ochrana proti �niku prost�edk� nebo exploit�m j�dra.  
- **Pou�it�**: Doporu�eno pro produk�n� prost�ed�.  
- **P��klad**:  
  ```powershell
  docker run --isolation=hyperv mcr.microsoft.com/windows/servercore:ltsc2022
  ```  

#### **b) Process Isolation**  

- **Princip**: Kontejnery sd�lej� j�dro hostitele (podobn� jako Linux kontejnery).  
- **Rizika**:  
  - Mo�nost �toku p�es zranitelnosti j�dra Windows.  
  - M�n� bezpe�n� ne� Hyper-V.  
- **Pou�it�**: Vhodn� pro v�voj a testov�n� na stejn� verzi OS.  

---

### **3. Zabezpe�en� obraz� (Images)**  

- **Pou��vejte d�v�ryhodn� z�kladn� obrazy**:  
  - Ofici�ln� Microsoft obrazy (nap�. `mcr.microsoft.com/windows/servercore`).  
  - Pravideln� aktualizujte pomoc� `docker pull`.  
- **Skenov�n� zranitelnost�**:  
  ```powershell
  docker scan mcr.microsoft.com/windows/servercore:ltsc2022
  ```  
  - N�stroje: Docker Scout, Trivy, Azure Container Registry Vulnerability Scanning.  
- **Minimalizace velikosti obrazu**:  
  - Odstra�te nepot�ebn� bal��ky a slu�by (nap�. PowerShell v produkci).  

---

### **4. Runtime Bezpe�nost**  

#### **a) Omezen� p��stupu**  

- **Nepou��vejte kontejnery jako Administrator**:  
  ```powershell
  docker run --user "ContainerUser" moje_aplikace
  ```  
- **Zak�z�n� privilegovan�ch kontejner�**:  
  Nepou��vejte `--privileged` (na Windows nen� podporov�no, ale relevantn� pro Linux kontejnery na WSL2).  

#### **b) Omezen� prost�edk�**  

- **Kv�ty CPU a RAM**:  
  ```powershell
  docker run --cpus=2 --memory=4GB moje_aplikace
  ```  
- **Read-only filesyst�m**:  
  ```powershell
  docker run --read-only -v C:\Data:C:\App\Data:ro moje_aplikace
  ```  

#### **c) S�t� a porty**  

- **Minimalizujte otev�en� porty**:  
  ```powershell
  docker run -p 80:80 --expose 80 web
  ```  
- **Pou��vejte Docker s�t� pro segmentaci**:  
  ```powershell
  docker network create interni_sit
  docker run --network=interni_sit database
  ```  

---

### **5. Zabezpe�en� hostitele**  

- **Aktualizace**: Pravideln� aktualizujte Windows Host a Docker Engine.  
- **Konfigurace Docker Daemonu**:  
  - Pou��vejte TLS pro vzd�len� p��stup.  
  - Omezen� p��stupu k Docker socketu (`docker.sock`).  
- **Windows Defender a Firewall**:  
  - Povolte kontrolu kontejner� v Windows Defenderu.  
  - Nakonfigurujte pravidla firewallu pro Docker (porty 2375/2376).  

---

### **6. Spr�va tajemstv� (Secrets)**  

- **Docker Swarm Secrets**:  
  ```powershell
  echo "Heslo123!" | docker secret create db_password -
  docker service create --secret db_password moje_sluzba
  ```  
- **Pro Kubernetes na Windows**:  
  - Pou��vejte Kubernetes Secrets nebo Azure Key Vault.  
- **Nikdy neukl�dejte tajemstv� v Dockerfile!**  

---

### **7. Monitorov�n� a audit**  

- **Logov�n� kontejner�**:  
  ```powershell
  docker run --log-driver=json-file --log-opt max-size=10m moje_aplikace
  ```  
- **N�stroje**:  
  - Azure Monitor, Elastic Stack, Splunk.  
  - Detekce anom�li� v s�ov�m provozu (nap�. Wireshark).  
- **Auditov�n�**:  
  - Sledujte Docker ud�losti: `docker events`.  
  - Kontrolujte audit logy Windows (`Event Viewer`).  

---

### **8. Windows-specifick� rizika**  

- **�toky p�es PowerShell**:  
  - Zak�zat nebo omezit PowerShell v produk�n�ch kontejnerech.  
- **Active Directory Integration**:  
  - Kontejnery p�ipojen� k AD pou��vejte pouze v d�v�ryhodn�ch s�t�ch.  
- **Shared Kernel �toky**:  
  - U Process Isolation hroz� exploitace j�dra Windows.  

---

### **9. N�stroje a Doporu�en�**  

- **Docker Bench for Security**:  
  Automatizovan� kontrola konfigurace dle CIS benchmark�.  
- **Azure Security Center**:  
  Detekuje hrozby v kontejnerech a obrazech.  
- **Best Practices**:  
  - Pou��vejte Hyper-V isolation v produkci.  
  - Omezujte p��stup pomoc� RBAC (Role-Based Access Control).  
  - Pravideln� rotujte tajemstv� a certifik�ty.  

---

### **10. Shrnut�**  

- **Izolace**: Hyper-V isolation je nutnost pro kritick� aplikace.  
- **Obrazy**: Skenujte a aktualizujte, pou��vejte minim�ln� z�klad.  
- **Pr�va**: Nejni��� mo�n� opr�vn�n� pro kontejnery a u�ivatele.  
- **S�t�**: Segmentace a minimalizace otev�en�ch port�.  
- **Windows specifika**: Pozor na integraci s AD, PowerShell a j�dro.  

**Doporu�en�**:  
- Pravideln� testujte bezpe�nost pomoc� n�stroj� jako Nessus nebo OpenSCAP.  
- Pro komplexn� aplikace vyu��vejte Kubernetes Security Policies (nap�. Pod Security Policies).  

--- 

**Viz tak�**:  
- [Docker Security Best Practices](https://docs.docker.com/engine/security/)  
- [Microsoft Container Security](https://learn.microsoft.com/en-us/virtualization/windowscontainers/security/)
