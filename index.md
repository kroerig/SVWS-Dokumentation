***SVWS - Dokumentation***
====================

![Open-api-logo-klein.png](./graphics/Open-api-logo-klein.png)

Das Projekt zur Schaffung einer Open:API-Schnittstelle für die Schulverwaltung NRW.


# Übersicht

**Das Projekt:** 
Die Schulverwaltungssoftware des Landes NRW wurde im Jahr 2000 erstmalig in Auftrag gegeben.
Mittlerweile ist die Software 20 Jahre alt und es besteht dringender Bedarf an Modernisierung.
Unter den bisherigen Entwicklern besteht der Konsens, dass es extrem schwierig sein wird, eine neue Software mit den bestehenden Ressourcen zu entwicklen, die den gleichen Funktionsumfang bietet, wie das jetzige Schild-NRW.

Aus diesem Grund die Idee: Entwicklung eines REST-Servers, der eine offene API-Schnittstelle zur Verfügung stellt.
Schild-NRW kann dann weiterhin in einer Übergangsphase auf die Datenbank zugreifen.
Sobald aber alle Services der REST-Schnittstelle zur Verfügung stehen, kann Schild-NRW dann nach und nur noch als GUI dienen.



![Übersicht-REST](./graphics/700px-Uebersicht-REST-Server-01.png)



In der Übersicht soll einen erster Eindruck vom zukünftigen Aufbau vermitteln.
Im Vordergrund steht die Kapselung von Datenbank, Core-API und GUI.


[Projektanforderungen](Projektanforderungen.md)

[FAQ](FAQ.md)


----