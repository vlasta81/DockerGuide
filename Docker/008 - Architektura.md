
# Architektura Dockeru (Windows)

---

### **1. Základní architektura Dockeru**  

Docker používá **klient-server model** s těmito komponentami:  
1. **Docker Client** (CLI/GUI): Umožňuje komunikaci s Docker Daemonem (příkazy jako `docker run`).  
2. **Docker Daemon** (`dockerd`): Hlavní služba spravující kontejnery, image, sítě a svazky.  
3. **Kontejnery**: Izolované prostředí pro běh aplikací.  
4. **Image**: Šablony pro vytváření kontejnerů.  
5. **Registry** (Docker Hub, Azure Container Registry): Úložiště imagí.  

---

### **2. Specifika architektury na Windows**  

#### **a) Docker Desktop for Windows**  

- Integruje Docker Engine s Windows prostředím.  
- Podporuje dva režimy:  
  - **Windows Containers**: Běží přímo na Windows kernelu.  
  - **Linux Containers**: Běží přes **WSL 2** (doporučeno) nebo **Hyper-V**.  

#### **b) Vrstvy architektury**  

1. **Aplikační vrstva**: Uživatelské nástroje (Docker CLI, Visual Studio).  
2. **Docker Engine**:  
   - `dockerd` (Docker Daemon).  
   - `containerd` (správa životního cyklu kontejnerů).  
3. **Virtuální vrstva**:  
   - Pro **Linux kontejnery**: WSL 2 nebo Hyper-V.  
   - Pro **Windows kontejnery**: Host Compute Service (HCS) a Windows kernel.  
4. **Hardwarová vrstva**: Fyzický server nebo VM.  

---

### **3. Klíčové komponenty pro Windows**  

#### **a) Windows Containers**  

- **Host Compute Service (HCS)**:  
  - Systémový nástroj pro správu izolace kontejnerů.  
  - Komunikuje s Windows kernelem.  
- **Storage a síťové ovladače**:  
  - **Storage**: Využívá NTFS svazky a bind mounts.  
  - **Sítě**: NAT, transparentní režim, L2 bridge.  

#### **b) Linux Containers**  

- **WSL 2 (Windows Subsystem for Linux)**:  
  - Umožňuje běh Linuxového kernelu na Windows.  
  - Lepší výkon a integrace než Hyper-V.  
- **Hyper-V**:  
  - Alternativa pro starší verze Windows bez WSL 2.  

---

### **4. Izolace kontejnerů na Windows**  

- **Procesní izolace** (Windows Containers):  
  - Kontejnery sdílejí kernel hostitele.  
  - Vyžaduje shodu **verze OS** (např. `ltsc2022`).  
- **Hyper-V izolace** (Windows/Linux Containers):  
  - Každý kontejner běží v lehké VM.  
  - Lepší bezpečnost, ale vyšší režie.  

---

### **5. Srovnání s Linuxovou architekturou**  

| **Parametr**          | **Windows**                          | **Linux**                   |  
|-----------------------|--------------------------------------|-----------------------------|  
| **Kernel**            | Windows kernel                       | Linux kernel                |  
| **Izolace**           | HCS nebo Hyper-V                     | namespaces + cgroups        |  
| **Velikost image**    | Až 5+ GB (Windows Server Core)       | Často pod 100 MB            |  
| **Použití**           | Legacy aplikace (IIS, .NET Framework)| Moderní stacky (Node.js, Python) |  

---

### **6. Komunikace mezi komponentami**  

1. Uživatel zadá příkaz přes **Docker CLI** (např. `docker run`).  
2. **Docker Client** předá požadavek **Docker Daemonu**.  
3. **Docker Daemon** komunikuje s:  
   - **containerd** (pro životní cyklus kontejnerů),  
   - **runc** (pro vytváření a správu kontejnerů),  
   - **HCS/WSL 2** (pro izolaci).  
4. Image se stáhnou z **registru** (pokud nejsou lokálně).  

---

### **7. Výhody architektury na Windows**  

- **Kombinace Windows a Linuxových kontejnerů**: Flexibilita pro hybridní aplikace.  
- **Integrace s Windows ekosystémem**:  
  - Podpora Active Directory, skupinových politik.  
  - Kompatibilita s nástroji jako PowerShell nebo SQL Server.  
- **Výkon**: WSL 2 minimalizuje režii oproti Hyper-V.  

---

### **8. Omezení**  

- **Velikost imagí**: Windows base image jsou objemné.  
- **Kompatibilita verzí**: Nutnost shody OS hostitele a kontejneru.  
- **Omezená komunita**: Méně imagí a tutoriálů pro Windows.  

---

### **9. Diagram architektury**  

```
+-------------------+  
| Docker Client     |  
| (CLI, VS Code)    |  
+-------------------+  
         ↓  
+-------------------+  
| Docker Daemon     |  
| (dockerd)         |  
+-------------------+  
         ↓  
+-------------------+     +-------------------+  
| Windows Containers|     | Linux Containers |  
| (HCS + Windows    |     | (WSL 2 / Hyper-V)|  
| kernel)           |     |                  |  
+-------------------+     +-------------------+  
         ↓                         ↓  
+-------------------+     +-------------------+  
| Fyzický hardware  |     | Virtuální vrstva  |  
| nebo VM           |     | (WSL 2/Hyper-V)   |  
+-------------------+     +-------------------+  
```

---

### **10. Užitečné zdroje**  

- **Dokumentace**:  
  - [Microsoft – Windows kontejnery](https://learn.microsoft.com/cs-cz/virtualization/windowscontainers/)  
  - [Docker – Architektura](https://docs.docker.com/get-started/overview/#docker-architecture)  
- **Nástroje**:  
  - **Docker Desktop Dashboard** (monitoring kontejnerů),  
  - **WSL 2** (pro optimalizaci Linuxových kontejnerů).  

---

**Shrnutí**: Architektura Dockeru na Windows kombinuje nativní Windows komponenty (HCS) s moderními technologiemi (WSL 2), což umožňuje běh jak Windows, tak Linuxových kontejnerů. Klíčové je správné nastavení izolace a kompatibility verzí OS.
