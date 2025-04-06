
# Kontejnery a shelly (Windows)

---

### **1. Kontejnery a shelly � Z�klady** 

- **Kontejner**: Izolovan� prost�ed� pro b�h aplikace, vytvo�en� z Docker image.  
- **Shell**: Rozhran� pro interakci s kontejnerem (nap�. `cmd`, `PowerShell`, `bash`).  
- **Kl��ov� sc�n��e**:  
  - Spou�t�n� aplikac� v kontejneru.  
  - Lad�n� a spr�va kontejner� pomoc� shellu.  
  - Automatizace skript� uvnit� kontejneru.  

---

### **2. Typy shel� na Windows**  

#### **a) Windows kontejnery**  

- **CMD (Command Prompt)**: Z�kladn� shell pro Windows (nap�. `docker run -it ... cmd`).  
- **PowerShell**: Pokro�il� skriptov�n� a spr�va (p��stup p�es `pwsh` nebo `powershell`).  
  ```powershell  
  docker exec -it <ID> pwsh  
  ```  

#### **b) Linux kontejnery**  

- **Bash/Sh**: V�choz� shelly pro Linuxov� kontejnery (vy�aduje WSL 2).  
  ```powershell  
  docker exec -it <ID> /bin/bash  
  ```  

---

### **3. Interakce s kontejnery**  

#### **a) Spu�t�n� interaktivn�ho shellu**  

- **P�i vytv��en� kontejneru**:  
  ```powershell  
  docker run -it mcr.microsoft.com/windows/servercore:ltsc2022 cmd  
  ```  
- **V b��c�m kontejneru**:  
  ```powershell  
  docker exec -it <ID> pwsh  
  ```  

#### **b) P��klad: IIS + PowerShell**  

1. Spus�te kontejner s IIS:  
   ```powershell  
   docker run -d -p 80:80 --name m�j-iis mcr.microsoft.com/windows/servercore/iis  
   ```  
2. P�ipojte se k PowerShellu:  
   ```powershell  
   docker exec -it m�j-iis pwsh  
   ```  
3. Ov��te slu�bu IIS:  
   ```powershell  
   Get-Service -Name W3SVC  
   ```  

---

### **4. Kl��ov� p��kazy pro pr�ci se shelly**  

| **P��kaz** | **Popis** |  
|------------|-----------|  
| `docker run -it <image> <shell>` | Spust� kontejner s interaktivn�m shellem. |  
| `docker exec -it <ID> <shell>` | P�ipoj� se k b��c�mu kontejneru. |  
| `docker attach <ID>` | P�ipoj� se k hlavn�mu procesu kontejneru (nap�. pro aplikace bez shellu). |  
| `docker cp <ID>:<cesta> <host_cesta>` | Zkop�ruje soubory mezi kontejnerem a hostitelem. |  

---

### **5. Specifika pro Windows kontejnery**  

- **Omezen�**:  
  - Verze OS v image mus� odpov�dat hostiteli (nap�. `ltsc2022`).  
  - Nelze spustit Linuxov� shell v Windows kontejneru.  
- **V�choz� shelly**:  
  - **Server Core**: `cmd` nebo `PowerShell`.  
  - **Nano Server**: Minimalizovan� `PowerShell Core`.  

---

### **6. �ast� probl�my a �e�en�**  

- **Probl�m**: *"Container exited immediately"*  
  - **P���ina**: Kontejner nem� b��c� proces (nap�. spu�t�n pouze shell, kter� se ukon��).  
  - **�e�en�**: Pou�ijte `docker run -it ...` pro interaktivn� re�im nebo p�idejte dlouho b��c� proces (nap�. `ping localhost -t`).  
- **Probl�m**: *"Shell not found"*  
  - **P���ina**: Image neobsahuje po�adovan� shell (nap�. `bash` v Windows kontejneru).  
  - **�e�en�**: Pou�ijte image s p�edinstalovan�m shellem (nap�. `mcr.microsoft.com/powershell`).  

---

### **7. Best Practices**  

1. **Minimalizujte interaktivn� shelly v produkci**: Pou��vejte je pouze pro lad�n�.  
2. **Automatizace skript�**: Spou�t�jte p��kazy p��mo p�es `docker exec` (nap�. `docker exec <ID> powershell -File script.ps1`).  
3. **Logov�n�**: Ukl�dejte v�stupy shelly do soubor� nebo extern�ch syst�m� (nap�. ELK stack).  
4. **Bezpe�nost**: Omezte pr�va u�ivatel� v kontejnerech (nap�. `--user` pro Linux).  

---

### **8. U�ite�n� p��klady**  

#### **a) Linuxov� kontejner s Bashem**  

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

### **9. N�stroje a zdroje**  

- **Docker Desktop Dashboard**: P�ehled b��c�ch kontejner� a p��m� p��stup k shellu p�es GUI.  
- **Visual Studio Code**: Roz���en� **Remote Containers** pro integrovan� v�voj v kontejnerech.  
- **Dokumentace**:  
  - [Microsoft � Windows Container Shells](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/manage-containers/interactive-container)  
  - [Docker � Exec Command](https://docs.docker.com/engine/reference/commandline/exec/)  

---

**Shrnut�**: Pr�ce se shelly v Docker kontejnerech na Windows umo��uje lad�n�, spr�vu a automatizaci. Kl��ov� je zvolit spr�vn� shell podle typu kontejneru (Windows/Linux) a dodr�ovat bezpe�nostn� doporu�en�. Pro interaktivn� �koly preferujte PowerShell nebo Bash, pro produkci automatizujte pomoc� skript�.
