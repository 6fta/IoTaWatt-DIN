
Poți adăuga datele JSON din IoTaWatt în Home Assistant folosind un RESTful Sensor. Acesta va interoga periodic URL-ul și va extrage valorile dorite.

1️⃣ Adaugă în configuration.yaml


sensor:
  - platform: rest
    name: "IoTaWatt Status"
    resource: "http://192.168.X.X/status?inputs=yes&outputs=yes&stats=yes"
    scan_interval: 10
    json_attributes:
      - stats
      - inputs
      - outputs

  - platform: template
    sensors:
      iotawatt_vrms:
        friendly_name: "IoTaWatt Vrms"
        unit_of_measurement: "V"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'inputs')[0]['Vrms'] }}"

      iotawatt_frequency:
        friendly_name: "IoTaWatt Frequency"
        unit_of_measurement: "Hz"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'stats')['frequency'] }}"

      iotawatt_power_watts:
        friendly_name: "IoTaWatt Power"
        unit_of_measurement: "W"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'inputs')[1]['Watts'] }}"

      iotawatt_power_factor:
        friendly_name: "IoTaWatt Power Factor"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'inputs')[1]['Pf'] }}"

      iotawatt_current:
        friendly_name: "IoTaWatt Current"
        unit_of_measurement: "A"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'outputs')[0]['value'] }}"


3️⃣ Verificare în Interfață
Mergi în Developer Tools -> States și caută sensor.iotawatt_status, sensor.iotawatt_vrms, etc. Dacă totul funcționează corect, vei vedea datele actualizate.

Dacă ai nevoie de alte valori din JSON, le poți extrage folosind value_template și state_attr().

Dacă Home Assistant nu extrage valorile din JSON, hai să facem niște verificări și ajustări:


✅ 1️⃣ Testează URL-ul în browser
Deschide http://iotawatt.local/status?inputs=yes&outputs=yes&stats=yes în browser și vezi dacă primești un răspuns JSON corect.

Dacă nu merge, înseamnă că Home Assistant nu poate accesa acest URL. 

Încearcă să folosești IP-ul dispozitivului în loc de hostname, de exemplu:

 "http://192.168.X.X/status?inputs=yes&outputs=yes&stats=yes"

✅ 2️⃣ Testează manual în Home Assistant
Accesează Developer Tools -> Template și adaugă acest cod pentru a vedea dacă JSON-ul este recunoscut corect:


{% set data = states('sensor.iotawatt_status') %}
{{ data }}

Dacă nu returnează nimic sau dă eroare, atunci sensor.iotawatt_status nu primește date corect.

✅ 3️⃣ Ajustare YAML pentru debugging
În loc de value_template, folosește doar json_attributes pentru a verifica dacă Home Assistant extrage datele.

Modifică configuration.yaml astfel:


sensor:
  - platform: rest
    name: "IoTaWatt Status"
    resource: "http://192.168.X.X/status?inputs=yes&outputs=yes&stats=yes"
    scan_interval: 10
    json_attributes:
      - stats
      - inputs

NU adăuga value_template! Acest senzor nu va avea o valoare vizibilă, dar va stoca toate atributele JSON.


✅ 4️⃣ Verificare în Home Assistant
După ce ai făcut modificările, repornește Home Assistant și mergi în Developer Tools -> States.
Caută sensor.iotawatt_status și vezi dacă are atributele stats și inputs.

Dacă datele apar acolo, putem adăuga senzori separați pentru extragerea valorilor. Dacă nu apare nimic, înseamnă că Home Assistant nu poate accesa IoTaWatt.

✅ 5️⃣ Creare senzori separați (dacă datele apar)
Dacă sensor.iotawatt_status are atribute, acum poți crea senzori separați:


sensor:
  - platform: template
    sensors:
      iotawatt_vrms:
        friendly_name: "IoTaWatt Vrms"
        unit_of_measurement: "V"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'inputs')[0]['Vrms'] }}"

      iotawatt_frequency:
        friendly_name: "IoTaWatt Frequency"
        unit_of_measurement: "Hz"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'stats')['frequency'] }}"

      iotawatt_power_watts:
        friendly_name: "IoTaWatt Power"
        unit_of_measurement: "W"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'inputs')[1]['Watts'] }}"


Cod actualizat

sensor:
  - platform: rest
    name: "IoTaWatt Status"
    resource: "http://192.168.X.X/status?inputs=yes&outputs=yes&stats=yes"
    scan_interval: 10
    json_attributes:
      - stats
      - inputs
      - outputs

  - platform: template
    sensors:
      iotawatt_vrms:
        friendly_name: "IoTaWatt Vrms"
        unit_of_measurement: "V"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'inputs')[0]['Vrms'] }}"

      iotawatt_frequency:
        friendly_name: "IoTaWatt Frequency"
        unit_of_measurement: "Hz"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'stats')['frequency'] }}"

      iotawatt_power_watts:
        friendly_name: "IoTaWatt Power"
        unit_of_measurement: "W"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'inputs')[1]['Watts'] }}"

      iotawatt_power_factor:
        friendly_name: "IoTaWatt Power Factor"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'inputs')[1]['Pf'] }}"

      iotawatt_current:
        friendly_name: "IoTaWatt Current"
        unit_of_measurement: "A"
        value_template: "{{ state_attr('sensor.iotawatt_status', 'outputs')[0]['value'] }}"







