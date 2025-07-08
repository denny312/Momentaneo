
ALTER TABLE Rifugio
ADD CONSTRAINT fk_rifugio_qualifica
FOREIGN KEY (Id) REFERENCES Qualifica(Id);


ALTER TABLE Rifugio
ADD CONSTRAINT fk_rifugio_atl
FOREIGN KEY (Id) REFERENCES ATL(Id);


ALTER TABLE Comune
ADD CONSTRAINT fk_comune_provincia
FOREIGN KEY (Id) REFERENCES Provincia(Id);

ALTER TABLE ATL
ADD CONSTRAINT fk_atl_provincia
FOREIGN KEY (Id) REFERENCES Provincia(Id);

ALTER TABLE Rifugio
ADD CONSTRAINT fk_rifugio_comune
FOREIGN KEY (Id) REFERENCES Comune(Id);



-- QUESTION 2 
-- Creazione tabella clienti
-- Creazione tabella clienti
CREATE TABLE clienti (
    id_cliente INT PRIMARY KEY IDENTITY(1,1),
    nome VARCHAR(50) NOT NULL,
    cognome VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE
);

-- Creazione tabella recensioni
CREATE TABLE recensioni (
    id_recensione INT PRIMARY KEY IDENTITY(1,1),
    titolo VARCHAR(100) NOT NULL,
    data_inserimento DATE NOT NULL,
    id_cliente INT NOT NULL,
    id_rifugio INT NOT NULL,
    data_soggiorno DATE NOT NULL,
    descrizione VARCHAR(255),
    voto INT NOT NULL,
    FOREIGN KEY (id_cliente) REFERENCES clienti(id_cliente),
    FOREIGN KEY (id_rifugio) REFERENCES Rifugio(Id)
);


{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.\\SQLEXPRESS;Database=RifugiPiemonte;User ID=sa;Password=Poncher2004!;TrustServerCertificate=True;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}


Install-Package Microsoft.EntityFrameworkCore.SqlServer
Install-Package Microsoft.EntityFrameworkCore.Tools
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design


Scaffold-DbContext "Server=ESAME-CSP-29;Database=RifugiPiemonte;User ID=sa;Password=Poncher2004!;TrustServerCertificate=True;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Context RifiugiPiemonteContext -DataAnnotations --Force


dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 8.0.0





using System.Diagnostics;
using Microsoft.AspNetCore.Mvc;
using RifugiPiemonte.Models;

namespace RifugiPiemonte.Controllers
{
    public class HomeController : Controller
    {
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger)
        {
            _logger = logger;
        }

        public IActionResult Index()
        {
            return View();
        }

        public IActionResult Privacy()
        {
            return View();
        }

        [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
        public IActionResult Error()
        {
            return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
        }
    }
}

 dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 8.0.0
Scaffold-DbContext "server=ESAME-CSP-19;Database=RifugiPiemonte;User ID=sa;Password=Poncher2004!;TrustServerCertificate=True;Trusted_Connection=True;"Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Context RifiugiPiemonteContext -DataAnnotations -Force


namespace RifugiPiemonte.Models
{
    public class ErrorViewModel
    {
        public string? RequestId { get; set; }

        public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);
    }
}












DOCKER COMPOSE 
✅ stack-traefik/docker-compose.yml (versione corretta)
yaml
Copia
Modifica
version: '3.8'

services:
  traefik:
    image: traefik:v3.0
    command:
      - --api.dashboard=true
      - --api.insecure=true
      - --entrypoints.web.address=:80
      - --providers.docker=true
      - --providers.docker.exposedbydefault=false
      - --providers.swarm=true
      - --providers.swarm.exposedbydefault=false
    ports:
      - "8880:80"      # Accesso ai servizi (via Traefik)
      - "8080:8080"    # Dashboard Traefik
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
      - traefik-net
    deploy:
      placement:
        constraints:
          - node.role == manager
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.api.rule=Host(`traefik.localhost`)"
        - "traefik.http.routers.api.service=api@internal"
        - "traefik.http.routers.api.entrypoints=web"

networks:
  traefik-net:
    external: true



✅ stack-flask/docker-compose.yml per Swarm e Traefik
Ecco la versione corretta del file:

yaml
Copia
Modifica
version: '3.8'

services:
  flask:
    image: my-flask-app:latest
    deploy:
      replicas: 5
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.flask.rule=Host(`flask.localhost`)"
        - "traefik.http.services.flask.loadbalancer.server.port=5000"
        - "traefik.http.routers.flask.entrypoints=web"
    networks:
      - traefik-net

networks:
  traefik-net:
    external: true

🛠️ Dockerfile corretto (multistage)
Sostituisci il tuo con questo:

dockerfile
Copia
Modifica
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY DotnetApp/*.csproj ./DotnetApp/
RUN dotnet restore ./DotnetApp/DotnetApp.csproj

COPY DotnetApp/ ./DotnetApp/
WORKDIR /src/DotnetApp
RUN dotnet publish -c Release -o /app/publish

FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build /app/publish .
EXPOSE 8088
ENTRYPOINT ["dotnet", "DotnetApp.dll"]
📁 Assicurati che:

DotnetApp/ contenga sia Program.cs che DotnetApp.csproj

🛠️ docker-compose.yml corretto (Swarm + Traefik)
Sostituisci il tuo con questo:

yaml
Copia
Modifica
version: '3.8'

services:
  dotnet:
    image: my-dotnet-app:latest
    deploy:
      replicas: 5
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.dotnet.rule=Host(`dotnet.localhost`)"
        - "traefik.http.services.dotnet.loadbalancer.server.port=8088"
        - "traefik.http.routers.dotnet.entrypoints=web"
    networks:
      - traefik-net

networks:
  traefik-net:
    external: true





public async Task<IActionResult> Ricerca(string searchString)
{
    // Usa ViewBag per mantenere il valore della ricerca
    ViewBag.SearchString = searchString;

    IQueryable<Rifugio> rifugi = _context.Rifugio
        .Include(r => r.IdAtlNavigation)
        .Include(r => r.IdComuneNavigation)
        .Include(r => r.IdQualificaNavigation);
    // Se il searchString non è vuoto o nullo, filtra per DenominazioneStruttura
    if (!string.IsNullOrEmpty(searchString))
    {
        rifugi = rifugi.Where(r => r.DenominazioneStruttura.Contains(searchString));
    }

    return View("Index", await rifugi.ToListAsync());
}


    <form asp-controller="Rifugi" asp-action="Ricerca" method="get">
        <p>
            Cerca per denominazione:
            <input type="text" name="searchString" value="@ViewBag.SearchString" />
            <input type="submit" value="Cerca" class="btn btn-primary" />
        </p>
    </form>









---------------------------------------------
@{
    ViewData["Title"] = "Dettagli Rifugio";
}

<h1>Dettagli del Rifugio: @Model.DenominazioneStruttura</h1>

<div>
    <strong>Denominazione:</strong> @Model.DenominazioneStruttura
</div>
<div>
    <strong>Indirizzo:</strong> @Model.Indirizzo
</div>
<div>
    <strong>Numero Civico:</strong> @Model.NroCivico
</div>
<div>
    <strong>Qualifica:</strong> @Model.IdQualificaNavigation
</div>
<div>
    <strong>Altitudine Struttura:</strong> @Model.AltitudineStruttura
</div>
<div>
    <strong>Telefono:</strong> @Model.Telefono
</div>
<div>
    <strong>Email:</strong> @Model.Email
</div>
<div>
    <strong>Sito Web:</strong> <a href="@Model.SitoWeb" target="_blank">@Model.SitoWeb</a>
</div>

<!-- Pulsante per lasciare una recensione solo se l'utente è autenticato -->
@if (User.Identity.IsAuthenticated)
{
    <form asp-controller="Recensioni" asp-action="Create" method="get">
        <input type="hidden" name="rifugioId" value="@Model.Id" />
        <input type="submit" value="Inserisci la tua recensione" class="btn btn-primary" />
    </form>
}
else
{
    <p><strong>Accedi per poter lasciare una recensione!</strong></p>
}

<!-- Link per tornare alla lista dei rifugi -->
<a asp-controller="Rifugi" asp-action="Index" class="btn btn-secondary">Torna all'elenco</a>















1. Calcolo risorse complessive (vBOM)
Anno Uno:
- vCPU totali: 800
- RAM totale: 800 GB
- Storage totale: 12 TB
- IOPS totali: 28.000
- Banda North/South: 2 Gbit/s
- Banda East/West: 60 Gbit/s
Anno Due:
- vCPU totali: 2.400
- RAM totale: 2.400 GB
- Storage totale: 36 TB
- IOPS totali: 84.000
- Banda North/South: 12 Gbit/s
- Banda East/West: 300 Gbit/s
2. Dimensionamento hardware (blade server)
Caratteristiche blade Skylake:
- 2 CPU da 20 core (40 core, 80 vCPU con HT)
- Limite blade: 32 vCPU utilizzabili
- RAM: 128 GB
- Banda NIC: 10 Gbps (una attiva)
Anno Uno:
- Blade minimi per vCPU: 25
- Blade richiesti con anti-affinity (1+1): 50
Anno Due:
- Blade minimi per vCPU: 75
- Blade richiesti con anti-affinity: 150
3. Scelte architetturali e motivazioni
Hypervisor scelto: Tipo 1 (bare-metal)
Motivazione: È installato direttamente sull'hardware, garantendo prestazioni elevate,
massimo isolamento tra le VM e maggiore stabilità rispetto agli hypervisor di tipo 2.
Essenziale per un ambiente enterprise con alti requisiti di affidabilità e performance.
Tecnica: Virtualizzazione completa (full virtualization) con supporto VT-x
Motivazione: Permette di eseguire sistemi operativi guest senza modifiche.
Ideale per l'ambiente Linux usato dall'azienda. L'uso dell'estensione Intel VT-x
garantisce alte prestazioni, grazie all'accelerazione hardware.
Prodotto consigliato: VMware vSphere / ESXi
Motivazione: Soluzione leader di mercato per ambienti enterprise. Offre:
- Alta disponibilità (HA), gestione dinamica delle risorse (DRS)
- Funzionalità avanzate come vMotion, snapshot, backup e clustering
- Ampio supporto, stabilità e documentazione tecnica
Alternativa open source per budget limitato: Proxmox VE
4. Voci di costo (CAPEX / OPEX)
CAPEX (una tantum):
- Blade server e chassis
- Storage centralizzato (SAN, NAS, SSD array)
- Licenze hypervisor (es. VMware vSphere)
- Networking (switch, cablaggi, ridondanza)
- Sistemi elettrici (UPS, ridondanze)
- Eventuali licenze di supporto Linux
OPEX (ricorrenti):
- Manutenzione hardware e supporto tecnico
- Aggiornamenti e rinnovi licenze software
- Consumi energetici e raffreddamento
- Formazione del personale tecnico
- Monitoraggio, sicurezza e continuità operativa
