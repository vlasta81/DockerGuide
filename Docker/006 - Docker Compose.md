
# Docker Compose (Windows)

---

### **1. Co je Docker Compose?**  

- Nástroj pro **definici a správu více kontejnerů** jako jedné aplikace.  
- Používá **YAML soubor** (`docker-compose.yml`), kde se popisují služby, sítě, svazky a závislosti.  
- **Výhody**:  
  - Zjednodušení orchestrace komplexních aplikací (např. web + databáze + cache).  
  - Možnost spouštět celý stack jedním příkazem.  

---

### **2. Klíčové funkce pro Windows**  

- **Podpora Windows kontejnerů**: Definice služeb pro Windows i Linux kontejnery (nutné správně nastavit `platform` v YAML).  
- **Integrace s Docker Desktopem**: Funguje out-of-the-box po instalaci Dockeru.  
- **Proměnné prostředí**: Konfigurace různých prostředí (vývoj vs. produkce).  

---

### **3. Instalace**  

- Docker Compose je **součástí Docker Desktopu pro Windows** (není potřeba samostatná instalace).  
- **Ověření funkčnosti**:  
  ```powershell  
  docker-compose --version  
  # Nebo (pro novější verze)  
  docker compose version  
  ```  

---

### **4. Struktura souboru `docker-compose.yml`**  

```yaml  
version: "3.9"  # Verze syntaxe  
services:  
  web:  
    image: mcr.microsoft.com/dotnet/aspnet:6.0  
    ports:  
      - "8080:80"  
    volumes:  
      - ./app:/app  
    depends_on:  
      - db  

  db:  
    image: mcr.microsoft.com/mssql/server:2022-latest  
    environment:  
      SA_PASSWORD: "Heslo123!"  
      ACCEPT_EULA: "Y"  
    volumes:  
      - sql_data:/var/opt/mssql  

volumes:  
  sql_data:  
```  

---

### **5. Základní příkazy**  

| Příkaz | Popis |  
|--------|-------|  
| `docker compose up` | Spustí všechny služby z `docker-compose.yml`. |  
| `docker compose up -d` | Spustí služby na pozadí (detached mode). |  
| `docker compose down` | Zastaví a odstraní kontejnery, sítě a svazky. |  
| `docker compose build` | Sestaví image podle definice v YAML. |  
| `docker compose logs` | Zobrazí logy všech služeb. |  
| `docker compose ps` | Zobrazí stav kontejnerů v stacku. |  

---

### **6. Příklad: Aplikace ASP.NET + SQL Server**  

1. Vytvořte `docker-compose.yml`:  
   ```yaml  
   version: "3.9"  
   services:  
     webapp:  
       image: můj-webový-image:latest  
       build: .  
       ports:  
         - "5000:80"  
       depends_on:  
         - database  

     database:  
       image: mcr.microsoft.com/mssql/server:2022-latest  
       environment:  
         SA_PASSWORD: "Heslo123!"  
         ACCEPT_EULA: "Y"  
   ```  
2. Spusťte:  
   ```powershell  
   docker compose up -d  
   ```  

---

### **7. Výhody a omezení**  

| **Výhody** | **Omezení** |  
|------------|-------------|  
| Jednoduchá správa více služeb. | Není vhodný pro produkční clustery (použijte Kubernetes). |  
| Možnost definovat prostředí (dev/test). | Omezená podpora pokročilých funkcí (např. automatické škálování). |  
| Portabilita mezi různými OS. | Nutnost kompatibility verzí (Windows vs. Linux image). |  

---

### **8. Časté problémy a řešení**  

- **Chyba portů**:  
  - *"Port is already allocated"* → Změňte port v `ports` (např. `"8080:80"` → `"8081:80"`).  
- **Nekompatibilní image**:  
  - Chyba: *"Image operating system mismatch"* → Přidejte do služby parametr `platform: windows` nebo `linux`.  
- **Práva k souborům ve svazcích**:  
  - V Docker Desktopu povolte sdílení složky (**Settings → Resources → File Sharing**).  

---

### **9. Best Practices**  

- **Verze syntaxe**: Vždy definujte `version` v YAML (např. `3.9`).  
- **Separace prostředí**: Používejte různé soubory pro dev/prod (`docker-compose.override.yml`).  
- **Proměnné prostředí**: Ukládejte citlivá data (hesla) do `.env` souboru.  
- **Minimalizace kontejnerů**: Každá služba = jeden kontejner (např. web, db, cache).  

---

### **10. Užitečné zdroje**  

- **Dokumentace**:  
  - [Oficiální dokumentace Docker Compose](https://docs.docker.com/compose/)  
  - [Microsoft: Docker Compose pro Windows](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/compose/)  
- **Nástroje**:  
  - **VS Code** (podpora YAML a Docker rozšíření).  
  - **Docker Desktop Dashboard** (přehled běžících služeb).  

---

**Tip**: Pro rychlejší vývoj využijte `docker compose watch` (experimentální funkce v Docker Desktopu) – automaticky přebuduje image při změně kódu.
