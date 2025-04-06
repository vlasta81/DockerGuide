# Docker (Windows)

---

### **1. Co je Docker?**  

- Platforma pro **vytváření, distribuci a spouštění aplikací v kontejnerech**.  
- **Kontejnery**: Izolované prostředí s aplikací a všemi jejími závislostmi (knihovny, systémové nástroje, konfigurace).  
- Výhody: Konzistence mezi prostředími (vývoj, testování, produkce), rychlost, škálovatelnost.

---

### **2. Docker na Windows**  

- **Docker Desktop for Windows**: Hlavní nástroj pro práci s Dockerem na Windows.  
- Podporuje dva režimy:  
  - **Windows Containers**: Kontejnery specifické pro Windows (např. .NET Framework aplikace).  
  - **Linux Containers**: Kontejnery s Linuxovým prostředím (běží přes WSL 2 nebo Hyper-V).  

---

### **3. Požadavky na systém**  

- **Minimální verze OS**:  
  - Windows 10/11 Pro, Enterprise, nebo Education (64-bit).  
  - Pro Windows 10/11 Home: Vyžaduje WSL 2 (Windows Subsystem for Linux).  
- **Virtualizace**: Musí být povolena v BIOS/UEFI.  
- Doporučené: 4 GB RAM, 64-bit procesor.

---

### **4. Instalace Dockeru na Windows**  

1. Stáhněte **Docker Desktop** z [oficiálních stránek](https://www.docker.com/products/docker-desktop).  
2. Spusťte instalační soubor a postupujte podle pokynů.  
3. Po instalaci:  
   - Ověřte funkčnost příkazem `docker --version`.  
   - Spusťte testovací kontejner: `docker run hello-world`.  
4. Pro Linux kontejnery: Povolte **WSL 2** v nastavení Docker Desktopu.

---

### **5. Klíčové pojmy**  

- **Image**: Šablona pro vytvoření kontejneru (např. `nginx`, `microsoft/dotnet`).  
- **Kontejner**: Běžící instance image.  
- **Dockerfile**: Skript pro automatizovanou build image.  
- **Docker Compose**: Nástroj pro definici a správu více kontejnerů (soubor `docker-compose.yml`).  

---

### **6. Základní příkazy**  

| Příkaz | Popis |  
|--------|-------|  
| `docker pull <image>` | Stáhne image z registru (např. Docker Hub). |  
| `docker run <image>` | Spustí kontejner z image. |  
| `docker ps` | Zobrazí běžící kontejnery. |  
| `docker stop <ID>` | Zastaví kontejner. |  
| `docker rm <ID>` | Smaže kontejner. |  
| `docker images` | Zobrazí seznam stažených imagí. |  
| `docker build -t <název> .` | Sestaví image z Dockerfile. |  

---

### **7. Windows vs. Linux Kontejnery**  

- **Windows kontejnery**:  
  - Vyžadují shodu verze OS hostitele a image (např. Windows Server 2019).  
  - Větší velikost (až několik GB).  
  - Vhodné pro legacy aplikace na .NET Framework.  
- **Linux kontejnery**:  
  - Běžnější, menší velikost.  
  - Lepší podpora open-source nástrojů.  

---

### **8. Příklady použití na Windows**  

#### **Vytvoření ASP.NET aplikace v kontejneru**  

1. Vytvořte `Dockerfile`:  
   ```dockerfile
   FROM mcr.microsoft.com/dotnet/aspnet:6.0
   WORKDIR /app
   COPY . .
   ENTRYPOINT ["dotnet", "MojeAplikace.dll"]
   ```  
2. Sestavte image:  
   ```bash
   docker build -t moje-aplikace .
   ```  
3. Spusťte kontejner:  
   ```bash
   docker run -d -p 8080:80 moje-aplikace
   ```  
4. Aplikace bude dostupná na `http://localhost:8080`.

---

### **9. Výhody a omezení**  

- **Výhody**:  
  - Eliminace problémů s kompatibilitou ("funguje to na mém počítači").  
  - Snadné testování a nasazování.  
- **Omezení**:  
  - Windows kontejnery mají omezenou komunitu a méně imagí.  
  - Vyšší nároky na místo na disku.  

---

### **10. Užitečné zdroje**  

- **Dokumentace**: [Docker Docs](https://docs.docker.com/), [Microsoft Learn](https://learn.microsoft.com).  
- **Komunita**: Fóra Stack Overflow, GitHub repozitáře.  
- **Kurzy**: Např. Docker Mastery na Udemy.  

--- 

**Tip**: Pro efektivní práci s Dockerem na Windows používejte WSL 2 – zlepší výkon a kompatibilitu s Linuxovými nástroji.
