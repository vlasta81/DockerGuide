
# Container Linking (Windows)

---

### **1. �vod**  

Container Linking je zastaral� metoda propojen� Docker kontejner�, kter� umo��uje komunikaci mezi nimi prost�ednictv�m alias� a sd�len� prom�nn�ch prost�ed�. A�koli je dnes nahrazena modern�j��mi technologiemi (Docker s�t�), znalost t�to funkce je u�ite�n� pro pr�ci se star��mi projekty. Na Windows funguje podobn� jako na Linuxu, ale s mo�n�mi omezen�mi v s�ov�ch re�imech.

---

### **2. Co je Container Linking?**  

- **Definice**: Mechanismus propojen� dvou nebo v�ce kontejner�, kdy jeden kontejner ("zdrojov�") poskytuje slu�by druh�mu ("c�lov�mu").  
- **Funkce**:  
  - Automatick� nastaven� prom�nn�ch prost�ed� v c�lov�m kontejneru (nap�. informace o portu, IP adrese).  
  - Aktualizace souboru `hosts` v c�lov�m kontejneru pro p�eklad aliasu na IP adresu.  
- **Stav**: **Zastaral�** (deprecated). Doporu�uje se pou��vat **Docker s�t�** (user-defined networks).

---

### **3. Jak to funguje na Windows?**  

- **Krok 1: Spu�t�n� zdrojov�ho kontejneru**  
  P��klad (PowerShell):  
  ```powershell
  docker run -d --name databaze -e MYSQL_ROOT_PASSWORD=heslo mysql:latest
  ```

- **Krok 2: Propojen� c�lov�ho kontejneru**  
  Pou�ijte flag `--link`:  
  ```powershell
  docker run -it --link databaze:mysqlalias aplikace-windows
  ```  
  - `databaze`: N�zev zdrojov�ho kontejneru.  
  - `mysqlalias`: Alias pro komunikaci z c�lov�ho kontejneru.  

- **V�stupy propojen�**:  
  - Prom�nn� prost�ed� v c�lov�m kontejneru: `MYSQLALIAS_PORT_3306_TCP_ADDR`, `MYSQLALIAS_PORT_3306_TCP_PORT`.  
  - Z�znam v `hosts` souboru: `172.17.0.2  mysqlalias`.

---

### **4. V�hody a nev�hody**  

| **V�hody**                          | **Nev�hody**                                  |  
|--------------------------------------|-----------------------------------------------|  
| Jednoduch� nastaven� pro mal� projekty. | Zastaral� (ne podporov�no v nov�ch verz�ch). |  
| Automatick� prom�nn� prost�ed�.      | Funguje pouze na v�choz� bridge s�ti.         |  
|                                      | Nekalibrovateln� pro komplexn� s�t�.          |  
|                                      | Riziko �niku citliv�ch dat p�es prom�nn�.     |  

**Windows-specifick� probl�my**:  
- Omezen� v s�ov�ch re�imech (nap�. NAT vs. transparentn� s�).  
- Kompatibilita pouze s Windows kontejnery p�i pou�it� Windows-specifick�ch s�ov�ch driver�.

---

### **5. Alternativy k Container Linking**  

- **Docker User-Defined Networks**:  
  ```powershell
  docker network create moje_sit
  docker run -d --name databaze --network moje_sit mysql:latest
  docker run -it --network moje_sit aplikace-windows
  ```  
  Kontejnery komunikuj� p��mo p�es jm�na (nap�. `ping databaze`).  

- **Docker Compose**: Automaticky vytv��� s�t� a propojuje kontejnery definovan� v `docker-compose.yml`.

---

### **6. Praktick� p��klad (Windows)**  

**Sc�n��**: Propojen� ASP.NET aplikace s SQL Server kontejnerem.  
1. Spu�t�n� SQL Serveru:  
   ```powershell
   docker run -d --name sqlserver -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Heslo123!" mcr.microsoft.com/mssql/server:2019-latest
   ```  
2. Spu�t�n� aplikace s propojen�m:  
   ```powershell
   docker run --link sqlserver:db -e "DB_CONNECTION=db" moje-aplikace
   ```  

---

### **7. Tipy pro �e�en� probl�m�**  

- **Kontrola propojen�**:  
  - `docker exec -it aplikace-windows ping db` (pozor: n�kter� Windows kontejnery nemaj� `ping` nainstalovan�).  
  - `docker inspect databaze` � ov��en� IP adresy.  
- **Prom�nn� prost�ed�**:  
  `docker exec aplikace-windows set` (zobraz� v�echny prom�nn�).  
- **Firewall**: Na Windows m��e blokovat komunikaci mezi kontejnery � zkontrolujte pravidla.

---

### **8. Shrnut�**  

- Container Linking je **zastaral� metoda**, nahrazen� s�t�mi.  
- Pro nov� projekty v�dy preferujte **Docker s�t�** nebo **Docker Compose**.  
- Na Windows dbejte na v�b�r s�ov�ho driveru a kompatibilitu kontejner� (Windows vs. Linux).  

--- 

**Doporu�en�**: Pro studium Dockeru na Windows prozkoumejte t�ma **Windows Containers vs. Linux Containers** a pr�ci s **Docker Compose**.
