
# Sestavovac� soubory (Windows)

---

### **1. �vod do sestavovac�ch soubor�** 

- **Sestavovac� soubory**: Definuj� proces tvorby Docker image (nap�. `Dockerfile`, `docker-compose.yml`, `.dockerignore`).  
- **��el**: Automatizace vytv��en� kontejner� s p�edem p�ipraven�mi z�vislostmi, konfigurac� a k�dem.  

---

### **2. Hlavn� typy soubor�**  

#### **a) Dockerfile**  

- **Popis**: Textov� soubor s instrukcemi pro sestaven� image.  
- **Kl��ov� instrukce pro Windows**:  
  ```dockerfile  
  FROM mcr.microsoft.com/windows/servercore:ltsc2022  # Base image  
  COPY ./app C:/app                                  # Kop�rov�n� soubor�  
  RUN powershell Install-WindowsFeature Web-Server   # Instalace komponent  
  CMD ["dotnet", "MojeAplikace.dll"]                # V�choz� p��kaz  
  ```  

#### **b) docker-compose.yml**  

- **Popis**: Definice v�ce slu�eb (kontejner�) a jejich vztah�.  
- **P��klad pro Windows**:  
  ```yaml  
  version: "3.9"  
  services:  
    web:  
      image: mcr.microsoft.com/dotnet/aspnet:6.0  
      ports:  
        - "8080:80"  
    db:  
      image: mcr.microsoft.com/mssql/server:2022-latest  
      environment:  
        SA_PASSWORD: "Heslo123!"  
  ```  

#### **c) .dockerignore**  

- **Popis**: Ur�uje soubory/slo�ky, kter� se **nekop�ruj�** do build kontextu.  
- **P��klad**:  
  ```  
  # .dockerignore  
  Dockerfile  
  **/*.log  
  tmp/  
  ```  

---

### **3. Specifika pro Windows**  

#### **a) Cesty a form�tov�n�**  

- Pou��vejte **backslash** (`\`) nebo **forward slash** (`/`) pro cesty:  
  ```dockerfile  
  COPY ./app C:/app    # Spr�vn�  
  COPY ./app C:\app    # Spr�vn�  
  ```  
- **Case-insensitive**: N�zvy soubor� nez�vis� na velikosti p�smen.  

#### **b) Base Image**  

- **Windows Server Core**: Pln� verze pro .NET Framework, IIS.  
- **Nano Server**: Ultra-lehk� (vhodn� pro .NET Core).  
- **D�le�it�**: Verze OS v image mus� odpov�dat hostiteli (nap�. `ltsc2022`).  

#### **c) PowerShell vs. CMD**  

- V�choz� shell je `cmd`; pro PowerShell pou�ijte:  
  ```dockerfile  
  SHELL ["powershell", "-Command"]  
  RUN Install-WindowsFeature Web-Server  
  ```  

---

### **4. P��kazy pro sestaven�**  

- **Sestaven� image z Dockerfile**:  
  ```powershell  
  docker build -t m�j-image:tag .  
  ```  
- **Sestaven� s vyu�it�m cache**:  
  ```powershell  
  docker build --no-cache -t m�j-image .  # Ignoruje cache  
  ```  
- **Multi-stage build**:  
  ```dockerfile  
  # F�ze 1: Build  
  FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build  
  COPY . .  
  RUN dotnet publish -c Release -o /app  

  # F�ze 2: Runtime  
  FROM mcr.microsoft.com/windows/servercore:ltsc2022  
  COPY --from=build /app /app  
  ```  

---

### **5. Best Practices**  

1. **Minimalizujte po�et vrstev**: Kombinujte p��kazy `RUN` pomoc� `&&`.  
2. **Optimalizujte build kontext**: Pou��vejte `.dockerignore`.  
3. **Verzov�n�**: V�dy specifikujte tagy (nap�. `:v1`, `:ltsc2022`).  
4. **Testujte v izolovan�m prost�ed�**: Ov��te kompatibilitu s hostitelem.  

---

### **6. �ast� probl�my a �e�en�**  

| **Probl�m** | **P���ina** | **�e�en�** |  
|-------------|-------------|-------------|  
| *"COPY failed"* | Neplatn� cesta nebo pr�va. | Ov��te form�t cest a `.dockerignore`. |  
| *"Image too large"* | Nadbyte�n� soubory ve vrstv�ch. | Pou�ijte multi-stage build a �ist�te do�asn� soubory. |  
| *"Container exits immediately"* | Chyb� dlouho b��c� proces. | P�idejte `CMD` s hlavn� slu�bou (nap�. `dotnet run`). |  
| *"OS version mismatch"* | Nesoulad tagu base image a hostitele. | Zm��te tag na odpov�daj�c� (nap�. `ltsc2022`). |  

---

### **7. U�ite�n� n�stroje**  

- **Visual Studio**: Automatick� generov�n� Dockerfile pro .NET aplikace.  
- **Docker Desktop Dashboard**: P�ehledn� spr�va image a kontejner�.  
- **dive**: Anal�za vrstev image pro optimalizaci velikosti.  

---

### **8. Odkazy a zdroje** 

- **Dokumentace**:  
  - [Microsoft � Windows Container Dockerfile](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/manage-docker/manage-windows-dockerfile)  
  - [Docker � Ofici�ln� dokumentace](https://docs.docker.com/engine/reference/builder/)  
- **Komunita**: F�ra Stack Overflow, GitHub repozit��e.  

---

**Shrnut�**: Sestavovac� soubory pro Docker na Windows vy�aduj� pozornost k form�tov�n� cest, v�b�ru base imag� a kompatibilit� verz� OS. Kl��ov� je vyu�it� multi-stage builds, minimalizace vrstev a spr�vn� nastaven� `.dockerignore`. Pro komplexn� aplikace pou�ijte `docker-compose.yml`.
