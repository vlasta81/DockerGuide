
# Příkazy (Windows)

---

### **1. Základní příkazy pro správu kontejnerů**  

| **Příkaz** | **Popis** | **Příklad** |  
|------------|-----------|-------------|  
| `docker run` | Spustí kontejner z image. | `docker run -it mcr.microsoft.com/windows/servercore:ltsc2022 cmd` |  
| `docker start` | Spustí zastavený kontejner. | `docker start <ID>` |  
| `docker stop` | Zastaví běžící kontejner. | `docker stop <ID>` |  
| `docker restart` | Restartuje kontejner. | `docker restart <ID>` |  
| `docker rm` | Smaže kontejner. | `docker rm <ID>` |  
| `docker ps` | Zobrazí seznam běžících kontejnerů. | `docker ps -a` (všechny kontejnery) |  
| `docker exec` | Spustí příkaz v běžícím kontejneru. | `docker exec -it <ID> pwsh` |  

---

### **2. Příkazy pro správu imagí**  

| **Příkaz** | **Popis** | **Příklad** |  
|------------|-----------|-------------|  
| `docker pull` | Stáhne image z registru. | `docker pull mcr.microsoft.com/windows/servercore:ltsc2022` |  
| `docker build` | Sestaví image z Dockerfile. | `docker build -t můj-image .` |  
| `docker images` | Zobrazí seznam stažených imagí. | `docker images` |  
| `docker rmi` | Smaže lokální image. | `docker rmi <ID>` |  
| `docker push` | Nahraje image do registru. | `docker push můj-účet/můj-image:verze` |  
| `docker tag` | Přejmenuje image nebo přidá tag. | `docker tag starý-image nový-image` |  

---

### **3. Síťování a svazky**  

| **Příkaz** | **Popis** | **Příklad** |  
|------------|-----------|-------------|  
| `docker network create` | Vytvoří síť pro kontejnery. | `docker network create můj-síť` |  
| `docker network ls` | Zobrazí seznam sítí. | `docker network ls` |  
| `docker volume create` | Vytvoří trvalý svazek. | `docker volume create můj-svazek` |  
| `docker volume ls` | Zobrazí seznam svazků. | `docker volume ls` |  
| `docker run -v` | Propojí svazek/složku s kontejnerem. | `docker run -v C:\Data:D:\Data <image>` |  
| `docker run -p` | Mapuje port hostitele na port kontejneru. | `docker run -p 8080:80 <image>` |  

---

### **4. Příkazy pro ladění a monitorování**  

| **Příkaz** | **Popis** | **Příklad** |  
|------------|-----------|-------------|  
| `docker logs` | Zobrazí logy kontejneru. | `docker logs <ID>` |  
| `docker inspect` | Zobrazí detaily o kontejneru/image. | `docker inspect <ID>` |  
| `docker stats` | Sleduje využití CPU, RAM a sítě. | `docker stats` |  
| `docker top` | Zobrazí procesy v kontejneru. | `docker top <ID>` |  
| `docker system prune` | Smaže nepoužívané objekty (kontejnery, sítě, cache). | `docker system prune -a` |  

---

### **5. Docker Compose (pro více kontejnerů)**  

| **Příkaz** | **Popis** | **Příklad** |  
|------------|-----------|-------------|  
| `docker compose up` | Spustí služby z `docker-compose.yml`. | `docker compose up -d` (na pozadí) |  
| `docker compose down` | Zastaví a odstraní všechny služby. | `docker compose down` |  
| `docker compose logs` | Zobrazí logy všech služeb. | `docker compose logs web` |  
| `docker compose build` | Sestaví image pro služby. | `docker compose build` |  

---

### **6. Windows-specifické příkazy a tipy**  

- **Přepínání mezi Linux a Windows kontejnery**:  
  - V Docker Desktopu klikněte pravým tlačítkem na ikonu Dockeru → **Switch to Windows containers**.  
- **WSL 2 integrace**:  
  - Pro lepší výkon Linuxových kontejnerů aktivujte v Docker Desktopu (**Settings → WSL Integration**).  
- **Příkazy v PowerShellu**:  
  - Pro práci s cestami používejte **backtick (`)** pro escape znaků:  
    ```powershell  
    docker run -v C:\Users:`\Data <image>  
    ```  

---

### **7. Časté problémy a řešení** 

- **Chyba: *"Port already in use"*** → Změňte port hostitele (`-p 8080:80`).  
- **Chyba: *"Container OS mismatch"*** → Stáhněte image se správným tagem (např. `ltsc2022`).  
- **Pomalé stahování imagí** → Použijte zrcadlo registru v `daemon.json`:  
  ```json  
  { "registry-mirrors": ["https://registry.example.com"] }  
  ```  

---

### **8. Užitečné zdroje**  

- **Dokumentace**:  
  - [Docker – Oficiální dokumentace](https://docs.docker.com/engine/reference/commandline/cli/)  
  - [Microsoft – Windows kontejnery](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/)  
- **Nástroje**:  
  - **Docker Desktop Dashboard** (GUI pro správu kontejnerů).  
  - **VS Code s Docker rozšířením** (automatické generování Dockerfile).  

---

**Shrnutí**: Základní příkazy Dockeru na Windows pokrývají správu kontejnerů, imagí, sítí a svazků. Klíčové je správné mapování portů, práce s Windows-specifickými image (např. `servercore`) a optimalizace pomocí WSL 2. Pro komplexní aplikace použijte **Docker Compose**.
