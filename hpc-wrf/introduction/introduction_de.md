# Einführung

Haben Sie sich jemals in einer Situation gefunden, in der Sie einen Regenschirm und keinen Regen oder spät in der Nacht auf einem Parkplatz ohne Snowbrush haben, weil Ihnen das gegeben wurde, was Sie als Fehlinformationen ansehen? Sind Sie müde von Ihrem lokalen Wettermann das Wetter falsch vorhersagen und möchten etwas dagegen tun? Nun, das ist ein Labor, in dem Sie vielleicht Wert finden. Heute besprechen wir Ihnen, wie Sie WRF auf OCI-Infrastruktur einrichten und ausführen. Ich werde auch ein Skript zur Verfügung stellen, wenn Sie WRF ausführen möchten, ohne zu lernen, wie Sie es einrichten.

Geschätzte Übungszeit: 90 Minuten

### Über WRF

WRF ist die Abkürzung für The Weather Research and Forecasting Model. Es ist ein mesoskales numerisches Wettervorhersagesystem, das sowohl für atmosphärische Forschung als auch für betriebliche Prognoseanwendungen entwickelt wurde. WRF gibt es heute zu einer Partnerschaft, die in den späten 90er Jahren zwischen dem National Center for Atmmosphärenforschung (NCAR), der National Oceanic and Atmmosphärischen Administration (repräsentiert von den National Centers for Environmental Prediction (NCEP) und dem Earth System Research Laboratory), der US Air Force, dem Naval Research Laboratory, der University of Oklahoma und der Federal Aviation Administration (FAA) begann. WRF ist Open Source und ist auf Github erhältlich. Mit WRF können Forscher das Wetter entweder anhand realer Daten simulieren, die über Beobachtungen und Analysen oder idealisierte atmosphärische Bedingungen gesammelt wurden. WRF bietet eine Betriebsprognose für eine Plattform, auf der viele Mitwirkende bei der Physik, Numerik und Datenassimilation geholfen haben. WRF ist derzeit in Betrieb bei NCEP und anderen Prognosezentren international, was eine ziemlich große Sache ist.

### Ziele

In dieser Übung behandelte Themen:

*   Abhängigkeiten und WRF herunterladen
*   Librarys für WRF/WPS werden kompiliert
*   WRF und WPS werden kompiliert
*   WRF-Domain mit Geogrid erstellen
*   GFS-Daten herunterladen und Ungrib und Metgrid ausführen
*   WRF mit echten Daten ausführen

### Voraussetzungen

In dieser Übung wird davon ausgegangen, dass Sie:

*   Free Tier- oder kostenpflichtiger Cloud-Account von Oracle

## Bestätigungen

*   **Autor** - Brian Bennett, Solution Engineer, Big Compute
*   **Zuletzt aktualisiert von/Datum** - Brian Bennett, Big Compute, Dezember 2020