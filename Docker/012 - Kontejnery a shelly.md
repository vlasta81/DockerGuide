
# Kontejnery a shelly (Windows)

---

### **1. Kontejnery a shelly – Základy** 

- **Kontejner**: Izolované prostøedí pro bìh aplikace, vytvoøené z Docker image.  
- **Shell**: Rozhraní pro interakci s kontejnerem (napø. `cmd`, `PowerShell`, `bash`).  
- **Klíèové scénáøe**:  
  - Spouštìní aplikací v kontejneru.  
  - Ladìní a správa kontejnerù pomocí shellu.  
  - Automatizace skriptù uvnitø kontejneru.  

---

### **2. Typy shelù na Windows**  

#### **a) Windows kontejnery**  

- **CMD (Command Prompt)**: Základní shell pro Windows (napø. `docker run -it ... cmd`).  
- **PowerShell**: Pokroèilé skriptování a správa (pøístup pøes `pwsh` nebo `powershell`).  
  ```powershell  
  docker exec -it <ID> pwsh  
  ```  

#### **b) Linux kontejnery**  

- **Bash/Sh**: Vıchozí shelly pro Linuxové kontejnery (vyaduje WSL 2).  
  ```powershell  
  docker exec -it <ID> /bin/bash  
  ```  

---

### **3. Interakce s kontejnery**  

#### **a) Spuštìní interaktivního shellu**  

- **Pøi vytváøení kontejneru**:  
  ```powershell  
  docker run -it mcr.microsoft.com/windows/servercore:ltsc2022 cmd  
  ```  
- **V bìícím kontejneru**:  
  ```powershell  
  docker exec -it <ID> pwsh  
  ```  

#### **b) Pøíklad: IIS + PowerShell**  

1. Spuste kontejner s IIS:  
   ```powershell  
   docker run -d -p 80:80 --name mùj-iis mcr.microsoft.com/windows/servercore/iis  
   ```  
2. Pøipojte se k PowerShellu:  
   ```powershell  
   docker exec -it mùj-iis pwsh  
   ```  
3. Ovìøte slubu IIS:  
   ```powershell  
   Get-Service -Name W3SVC  
   ```  

---

### **4. Klíèové pøíkazy pro práci se shelly**  

| **Pøíkaz** | **Popis** |  
|------------|-----------|  
| `docker run -it <image> <shell>` | Spustí kontejner s interaktivním shellem. |  
| `docker exec -it <ID> <shell>` | Pøipojí se k bìícímu kontejneru. |  
| `docker attach <ID>` | Pøipojí se k hlavnímu procesu kontejneru (napø. pro aplikace bez shellu). |  
| `docker cp <ID>:<cesta> <host_cesta>` | Zkopíruje soubory mezi kontejnerem a hostitelem. |  

---

### **5. Specifika pro Windows kontejnery**  

- **Omezení**:  
  - Verze OS v image musí odpovídat hostiteli (napø. `ltsc2022`).  
  - Nelze spustit Linuxovı shell v Windows kontejneru.  
- **Vıchozí shelly**:  
  - **Server Core**: `cmd` nebo `PowerShell`.  
  - **Nano Server**: Minimalizovanı `PowerShell Core`.  

---

### **6. Èasté problémy a øešení**  

- **Problém**: *"Container exited immediately"*  
  - **Pøíèina**: Kontejner nemá bìící proces (napø. spuštìn pouze shell, kterı se ukonèí).  
  - **Øešení**: Pouijte `docker run -it ...` pro interaktivní reim nebo pøidejte dlouho bìící proces (napø. `ping localhost -t`).  
- **Problém**: *"Shell not found"*  
  - **Pøíèina**: Image neobsahuje poadovanı shell (napø. `bash` v Windows kontejneru).  
  - **Øešení**: Pouijte image s pøedinstalovanım shellem (napø. `mcr.microsoft.com/powershell`).  

---

### **7. Best Practices**  

1. **Minimalizujte interaktivní shelly v produkci**: Pouívejte je pouze pro ladìní.  
2. **Automatizace skriptù**: Spouštìjte pøíkazy pøímo pøes `docker exec` (napø. `docker exec <ID> powershell -File script.ps1`).  
3. **Logování**: Ukládejte vıstupy shelly do souborù nebo externích systémù (napø. ELK stack).  
4. **Bezpeènost**: Omezte práva uivatelù v kontejnerech (napø. `--user` pro Linux).  

---

### **8. Uiteèné pøíklady**  

#### **a) Linuxovı kontejner s Bashem**  

```powershell  
docker run -it ubuntu /bin/bash  
# V Bashe:  
apt update && apt install -y curl  
```  

#### **b) PowerShell v Nano Serveru**  

```powershell  
docker run -it mcr.microsoft.com/windows/nanoserver:ltsc2022 pwsh  
```  

---

### **9. Nástroje a zdroje**  

- **Docker Desktop Dashboard**: Pøehled bìících kontejnerù a pøímı pøístup k shellu pøes GUI.  
- **Visual Studio Code**: Rozšíøení **Remote Containers** pro integrovanı vıvoj v kontejnerech.  
- **Dokumentace**:  
  - [Microsoft – Windows Container Shells](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/manage-containers/interactive-container)  
  - [Docker – Exec Command](https://docs.docker.com/engine/reference/commandline/exec/)  

---

**Shrnutí**: Práce se shelly v Docker kontejnerech na Windows umoòuje ladìní, správu a automatizaci. Klíèové je zvolit správnı shell podle typu kontejneru (Windows/Linux) a dodrovat bezpeènostní doporuèení. Pro interaktivní úkoly preferujte PowerShell nebo Bash, pro produkci automatizujte pomocí skriptù.
