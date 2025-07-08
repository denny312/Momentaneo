
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
    "DefaultConnection": "Server=.\\SQLEXPRESS;Database=RifugiPiemonte;User ID=sa;Password=rootable123!;TrustServerCertificate=True;"
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

