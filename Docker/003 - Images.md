
# Docker Images (Windows)

---

### **1. Co je Docker Image?**  

- **Image** = Šablona pro vytvoření kontejneru.  
- Obsahuje vše potřebné pro běh aplikace: kód, runtime, knihovny, systémové nástroje, konfiguraci.  
- **Vrstvená struktura**: Image se skládá z vrstev (layers), které se ukládají do cache pro efektivní rebuildování.  
- **Zdroje imagí**:  
  - Veřejné registry (Docker Hub, Microsoft Container Registry).  
  - Privátní registry (Azure Container Registry, self-hostované).  

---

### **2. Specifika Windows Docker Images**  

- **Windows vs. Linux Images**:  
  - **Windows Images** jsou optimalizovány pro Windows kontejnery a obsahují Windows-specifické komponenty (např. .NET Framework, IIS).  
  - **Závislost na verzi OS**: Verze image musí odpovídat verzi Windows hostitele (např. `ltsc2019`, `ltsc2022`).  
- **Oficiální Windows Images od Microsoftu**:  
  - `mcr.microsoft.com/windows/servercore` – minimální Windows Server Core.  
  - `mcr.microsoft.com/windows/nanoserver` – ultra-lehká varianta.  
  - `mcr.microsoft.com/dotnet/framework/aspnet` – pro ASP.NET aplikace.  

---

### **3. Vytvoření Docker Image pro Windows**  

#### **Kroky**:  

1. Vytvořte **Dockerfile** (textový soubor s instrukcemi).  
2. Definujte základní image (např. `FROM mcr.microsoft.com/dotnet/framework/aspnet:4.8`).  
3. Přidejte vlastní kód a konfiguraci (pomocí `COPY`, `RUN`, `WORKDIR`).  
4. Sestavte image příkazem:  
   ```powershell
   docker build -t můj-image:název .
   ```  

#### **Příklad Dockerfile pro ASP.NET**:  

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.8
WORKDIR /inetpub/wwwroot
COPY ./MojeAplikace .
```  

---

### **4. Správa Docker Images na Windows**  

#### **Klíčové příkazy**:  

| Příkaz | Popis |  
|--------|-------|  
| `docker images` | Zobrazí seznam stažených imagí. |  
| `docker pull <image>` | Stáhne image z registru. |  
| `docker rmi <ID>` | Smaže lokální image. |  
| `docker tag <ID> <nový-název>` | Přejmenuje image nebo přidá tag. |  
| `docker save -o <soubor.tar> <image>` | Exportuje image do souboru. |  
| `docker load -i <soubor.tar>` | Načte image ze souboru. |  

---

### **5. Best Practices pro Windows Images**  

- **Minimalizujte velikost**:  
  - Používejte `Nano Server` nebo `Server Core` místo plné Windows image.  
  - Odstraňte dočasné soubory v jednom layeru (např. kombinací `RUN` a `&&` v Dockerfile).  
- **Bezpečnost**:  
  - Aktualizujte base image kvůli opravám zabezpečení.  
  - Používejte oficiální image od Microsoftu.  
- **Tagování**:  
  - Vždy specifikujte verzi (např. `:ltsc2022`, `:4.8`).  
  - Nepoužívejte `latest` v produkčním prostředí.  

---

### **6. Časté problémy a řešení**  

- **Nekompatibilita verzí**:  
  - Chyba: *"The container operating system does not match the host operating system"*.  
  - Řešení: Stáhněte image s tagem odpovídajícím verzi Windows hostitele (ověřte pomocí `winver`).  
- **Velikost image**:  
  - Windows base image může mít i **5+ GB**.  
  - Řešení: Použijte multi-stage build nebo minimalizujte vrstvy.  

---

### **7. Příklady použití**  

#### **Spuštění IIS v kontejneru**:  

1. Stáhněte image:  
   ```powershell
   docker pull mcr.microsoft.com/windows/servercore/iis
   ```  
2. Spusťte kontejner:  
   ```powershell
   docker run -d -p 80:80 --name můj-iis mcr.microsoft.com/windows/servercore/iis
   ```  
3. Ověřte na `http://localhost`.  

---

### **8. Užitečné zdroje**  

- **Dokumentace**:  
  - [Microsoft Container Registry](https://mcr.microsoft.com/) – oficiální Windows images.  
  - [Dockerfile reference](https://docs.docker.com/engine/reference/builder/).  
- **Nástroje**:  
  - **Docker Desktop** (integrovaná podpora pro Windows).  
  - **Visual Studio** (možnost generovat Dockerfile automaticky).  

---

**Tip**: Pro zrychlení práce s Windows images používejte **WSL 2** (Windows Subsystem for Linux) – umožňuje efektivnější správu vrstev a cache.
