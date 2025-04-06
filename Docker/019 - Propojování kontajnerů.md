
# Container Linking (Windows)

---

### **1. Úvod**  

Container Linking je zastaralá metoda propojení Docker kontejnerù, která umoòuje komunikaci mezi nimi prostøednictvím aliasù a sdílení promìnnıch prostøedí. Aèkoli je dnes nahrazena modernìjšími technologiemi (Docker sítì), znalost této funkce je uiteèná pro práci se staršími projekty. Na Windows funguje podobnì jako na Linuxu, ale s monımi omezeními v síovıch reimech.

---

### **2. Co je Container Linking?**  

- **Definice**: Mechanismus propojení dvou nebo více kontejnerù, kdy jeden kontejner ("zdrojovı") poskytuje sluby druhému ("cílovému").  
- **Funkce**:  
  - Automatické nastavení promìnnıch prostøedí v cílovém kontejneru (napø. informace o portu, IP adrese).  
  - Aktualizace souboru `hosts` v cílovém kontejneru pro pøeklad aliasu na IP adresu.  
- **Stav**: **Zastaralé** (deprecated). Doporuèuje se pouívat **Docker sítì** (user-defined networks).

---

### **3. Jak to funguje na Windows?**  

- **Krok 1: Spuštìní zdrojového kontejneru**  
  Pøíklad (PowerShell):  
  ```powershell
  docker run -d --name databaze -e MYSQL_ROOT_PASSWORD=heslo mysql:latest
  ```

- **Krok 2: Propojení cílového kontejneru**  
  Pouijte flag `--link`:  
  ```powershell
  docker run -it --link databaze:mysqlalias aplikace-windows
  ```  
  - `databaze`: Název zdrojového kontejneru.  
  - `mysqlalias`: Alias pro komunikaci z cílového kontejneru.  

- **Vıstupy propojení**:  
  - Promìnné prostøedí v cílovém kontejneru: `MYSQLALIAS_PORT_3306_TCP_ADDR`, `MYSQLALIAS_PORT_3306_TCP_PORT`.  
  - Záznam v `hosts` souboru: `172.17.0.2  mysqlalias`.

---

### **4. Vıhody a nevıhody**  

| **Vıhody**                          | **Nevıhody**                                  |  
|--------------------------------------|-----------------------------------------------|  
| Jednoduché nastavení pro malé projekty. | Zastaralé (ne podporováno v novıch verzích). |  
| Automatické promìnné prostøedí.      | Funguje pouze na vıchozí bridge síti.         |  
|                                      | Nekalibrovatelné pro komplexní sítì.          |  
|                                      | Riziko úniku citlivıch dat pøes promìnné.     |  

**Windows-specifické problémy**:  
- Omezení v síovıch reimech (napø. NAT vs. transparentní sí).  
- Kompatibilita pouze s Windows kontejnery pøi pouití Windows-specifickıch síovıch driverù.

---

### **5. Alternativy k Container Linking**  

- **Docker User-Defined Networks**:  
  ```powershell
  docker network create moje_sit
  docker run -d --name databaze --network moje_sit mysql:latest
  docker run -it --network moje_sit aplikace-windows
  ```  
  Kontejnery komunikují pøímo pøes jména (napø. `ping databaze`).  

- **Docker Compose**: Automaticky vytváøí sítì a propojuje kontejnery definované v `docker-compose.yml`.

---

### **6. Praktickı pøíklad (Windows)**  

**Scénáø**: Propojení ASP.NET aplikace s SQL Server kontejnerem.  
1. Spuštìní SQL Serveru:  
   ```powershell
   docker run -d --name sqlserver -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Heslo123!" mcr.microsoft.com/mssql/server:2019-latest
   ```  
2. Spuštìní aplikace s propojením:  
   ```powershell
   docker run --link sqlserver:db -e "DB_CONNECTION=db" moje-aplikace
   ```  

---

### **7. Tipy pro øešení problémù**  

- **Kontrola propojení**:  
  - `docker exec -it aplikace-windows ping db` (pozor: nìkteré Windows kontejnery nemají `ping` nainstalovanı).  
  - `docker inspect databaze` – ovìøení IP adresy.  
- **Promìnné prostøedí**:  
  `docker exec aplikace-windows set` (zobrazí všechny promìnné).  
- **Firewall**: Na Windows mùe blokovat komunikaci mezi kontejnery – zkontrolujte pravidla.

---

### **8. Shrnutí**  

- Container Linking je **zastaralá metoda**, nahrazená sítìmi.  
- Pro nové projekty vdy preferujte **Docker sítì** nebo **Docker Compose**.  
- Na Windows dbejte na vıbìr síového driveru a kompatibilitu kontejnerù (Windows vs. Linux).  

--- 

**Doporuèení**: Pro studium Dockeru na Windows prozkoumejte téma **Windows Containers vs. Linux Containers** a práci s **Docker Compose**.
