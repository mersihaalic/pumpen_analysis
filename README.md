# Früherkennung von Pumpenausfällen aus Sensordaten

**Autorin:** Mersiha Alic · Data Analyst (Greenbootcamps) · M.Sc. Geologie

## Fragestellung
Kann man aus den Sensordaten einer Industriepumpe einen Ausfall erkennen, **bevor** er passiert?
Und wie viel Vorwarnzeit gewinnt man damit für die Wartungsplanung?

Das ist die Grundidee von Condition Monitoring: Abweichungen bei Vibration, Temperatur, Druck und Durchfluss früh erkennen, statt auf den Ausfall zu warten.

## Daten
[Pump Sensor Data](https://www.kaggle.com/datasets/nphantawee/pump-sensor-data) (Kaggle):
- 52 Sensoren einer großen Industriepumpe, Messung jede Minute, 01.04.–31.08.2018 (220.320 Zeilen)
- 7 Ausfälle; Spalte `machine_status` = NORMAL / BROKEN / RECOVERING

`sensor.csv` ist nicht im Repository enthalten (Kaggle). Bitte von Kaggle herunterladen und neben das Notebook legen bzw. in Colab hochladen.

## Vorgehen
1. **Datenqualität:** 2 unbrauchbare Sensoren entfernt (`sensor_15` leer, `sensor_50` 35 % fehlend), Lücken gefüllt
2. **Sensorverlauf:** Sensoren mit der größten Veränderung vor Ausfällen; Erkennung mehrerer **Betriebszustände** (z. B. `sensor_36` springt zwischen festen Niveaus)
3. **Health Score (Stundenwerte):** Abweichung vom eigenen Normalbetrieb der letzten 7 Tage; Warnschwelle = höchstens 1 % Warnzeit im Normalbetrieb
4. **Kurzfristige Vorwarnung (Minutenwerte):** Verhalten in den 6 Stunden vor jedem Ausfall
5. **Random Forest „Ausfall in 24 h“:** relative Merkmale, ehrlich bewertet mit *leave-one-failure-out* (7 Durchläufe, je ein Ausfall ungesehen)

## Ergebnisse
| Methode | Erkannte Ausfälle | Vorwarnzeit | Fehlalarm-Zeit |
| --- | --- | --- | --- |
| Health Score (Stunden) | 1 von 7 (28.06.) | 18 h | 1,1 h pro Woche |
| Kurzfristig (Minuten) | __ von 7 | __ min (Median) | __ h pro Woche |
| Random Forest (24 h) | 0 von 7 | – | – |

![Health Score](grafik2_health_score.png)

## Fazit
- Die meisten Ausfälle in diesen Daten kündigen sich **nicht** Stunden vorher an – sie kommen abrupt. Nur der Ausfall vom 28.06. zeigt eine klare Vorwarnung (Health Score steigt etwa einen Tag vorher).
- Die Pumpe läuft in mehreren Betriebszuständen; Zustandswechsel erhöhen das Grundrauschen. Nächster Schritt: Health Score **je Betriebszustand** (Zustände per Clustering erkennen).
- Mit 7 Ausfällen ist das ein Machbarkeitsnachweis. Viele Kaggle-Lösungen mit ~99 % Accuracy erkennen nur, dass die Pumpe *bereits* ausgefallen ist – hier wurde bewusst nur echte Vorhersage bewertet.
- Für eine zuverlässige Frühwarnung braucht es gezielte Messgrößen (Vibration, Lagertemperatur) und Daten vieler Pumpen – der Ansatz von Condition-Monitoring-Systemen wie KSB Guard.

## Ausführen
 `pumpen_analyse.ipynb` öffnen, `sensor.csv` hochladen, *Laufzeit → Alle ausführen*.
Lokal:
```bash
pip install pandas numpy matplotlib seaborn scikit-learn
python pumpen_analyse.py
```

## Tools
Python · pandas · NumPy · Matplotlib · seaborn · scikit-learn · 
