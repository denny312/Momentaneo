
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
CREATE TABLE clienti (
    id_cliente INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(50) NOT NULL,
    cognome VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE
);

-- Creazione tabella recensioni
CREATE TABLE recensioni (
    id_recensione INT PRIMARY KEY AUTO_INCREMENT,
    titolo VARCHAR(100) NOT NULL,
    data_inserimento DATE NOT NULL,
    id_cliente INT NOT NULL,
    data_soggiorno DATE NOT NULL,
    descrizione TEXT,
    voto INT CHECK (voto >= 1 AND voto <= 5),
    FOREIGN KEY (id_cliente) REFERENCES clienti(id_cliente)
);
