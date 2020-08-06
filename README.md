# Bearbeitung von Lastgangdaten mit Codelos.io restful APIs

## 1. Einführung

   ### 1. 1. Codelos.io Restful APIs für Energiewirtschaft

Codelos.io bietet eine Reihe von Funktionalitäten für Entwickler in der Enenrgiewirtschaft. Diese ermöglichen es, dass Entwickler sich auf die Kernfunktionalitäten deren Produkten zu fokusieren und eine Menge Zeit und Geld zu sparen.

Weitere Infos über die Restful APIs von Codelos.io finden Sie unter:<br>
https://developper.codelos.io

Die Dokumentation zu diese APIs finden Sie unter:<br>
https://developper.codelos.io/doku

Um direkt loszulegen können Sie die Code-beispiele für die folgende Programmiersprachen nutzen:
* [Python](https://developper.codelos.io/doku)
* [PHP](https://developper.codelos.io/doku)
* [Javascript](https://developper.codelos.io/doku)
* [Java](https://developper.codelos.io/doku)

Diese Doku plus weitere nutzliche Inhalte für Entwickler finden Sie unter:<br>
https://github.com

   ### 1. 2.  Authentifizierung

Authentification nach oAuth methode, mit Benutzername und Passwort:


```python
# API end point
api_endpoint = 'http://127.0.0.1:1880'
function = 'oauth/token'

# credencials
params = '?username={}&password={}'.format(urllib.parse.quote('louqmane@gharnati.com'), urllib.parse.quote('Start-123'))

# call oauth
url = api_endpoint + '/' + function + params 
response = requests.get(url )
```

Die Funktion ```oauth/auth``` gibt einen Bearer Token zurück der in den weiteren Aufrufen der Funktion verwendet werden kann.


```python
# token
token = json.loads(response.text)
token
```

   ### 1. 3.  Loslegen mit dem ersten Aufruf

In dem folgeden Code_Beipiel werden wir mit der funktion ```parse``` der API einen Lastgang aus einer csv Datei einlesen und als JSOn zurückgeben.


```python
# parse load curve function
function = 'parse'
url = api_endpoint + '/' + function

# get file
filepath= '/Users/louq/Documents/projects/NoteBookTimeSeries/webinar/csv/get_started.csv'
with open(filepath) as fp:
    file_data = fp.read()
    file = {'file': ('file.csv', file_data,'text/csv')}

#call
rslt = requests.post(url = url, data ={'token' : token, 'response_format' : 'json', 'file': 'file.csv',}, files = file )
```

JSON response in eine dataframe einlesen und ausgeben


```python
# read json
jsn = json.loads(json.loads(rslt.text))
df = pd.DataFrame(jsn[0]['data'])
df[0] = pd.to_datetime(df[0],unit='ms')

# name columns and print
df.columns= ['datetime', 'value']

# print
df.head(15)
```

Wir packen den Code in einer Funktion und fügen weitere Optionen für die nächsten Beispiele in der Doku


```python
def parse(path, fix_time_stamps= True, get_meta = True, fill_missing_values='no_fill', plot=False):
    # parse load curve function
    function = 'parse'
    url = api_endpoint + '/' + function

    # get file
    with open(filepath) as fp:
        file_data = fp.read()
        file = {'file': ('file.csv', file_data,'text/csv')}

    #call
    rslt = requests.post(url = url, data ={'token' : token, 'file': 'file.csv', 'force_read' : fix_time_stamps, 'fill_missing_values': fill_missing_values}, files = file )

    #check http error responses
    if rslt.status_code != 200:
        print (rslt.status_code, rslt.reason, rslt.text)
    else:
        if get_meta:
            # read meta data
            meta_data = json.loads(json.loads(rslt.text))
            del meta_data[0]['data'] 
            print(json.dumps(meta_data[0], indent=4, sort_keys=True))

        #read time series
        jsn = json.loads(json.loads(rslt.text))
        df = pd.DataFrame(jsn[0]['data'])
        df[0] = pd.to_datetime(df[0],unit='ms')

        # name columns and print
        df.columns= ['datetime', 'value']
        if plot:
            df.plot(x='datetime')
        else:
            print (df.head(30))
```

## 2. Lastgang-zeitreihen erkennen und einlesen

### 2. 1. Zeitreihe lokalisieren

Beispiel:
Der Lastgang unten ist ein Typische Beispiel von eine Lastgang-Datei wo die Werte nicht direkt in der ersten Zeilen/Spalten zu finden sind. 

<img width="800px" style="margin-left:0" src="https://previews.dropbox.com/p/thumb/AA5_dTKarpI8vW12sPJT18gFSnA_BzacnoifwCfkRbJByqBxvP5i5rTl3KaP5_LUzu1jMV24fe-4m__3j1bzdqxoSGYmkbBrnKrQMf70XUCsegi37iPDwMo9pCwFMvSkSacH3K1JNWfNT0IinIHLrOsnLiOtuKO-6IwizbOz9akqOZPzdyTJ0XJjSjYqPLQwrTUVDwaDtCZcOkJpzPk3irjTv-7JsJ9jZElXQpqjzMU7Cc9hS15vt_kwqmTIHzeXrGz5ZA3cHO2zG3YzFkghUwOIAK9IQTiVjMSps_7aT2_7vhGJuzeqDapv2zwcwZWDNPBOYxytkynz_DAD6gLZeTWKtXY2Q5IfIXnCA-KEMUKyhA/p.png?fv_content=true&size_mode=5" />

Die API-Funktion ```/parse``` sucht den ersten Zeitstempel in der Datei, überprüft ob es sich um eine zeitreihe handelt und dann liest die Werte.

Darüber hinaus berechnet die Funktion die Kennzahlen des Lastgangs. 


```python
parse(filepath)
```

### 2. 2. Unterschiedlicher Zeitstämpel-Formate erkennen und einlesen

In den folgenden Beispiele sieht man unterschiedliche Zeitstempel-formate für Lastgänge. In der Praxis tauchen sehr viele Formate auf. Dies sind immer von herkunft der Daten abhängig.

Unterschiedliche EDMs nutzen unterschiedliche Export-Formate. Daten die direkt aus einer Datenbank exportiert wurden können wiederum auch andere Formate haben.

Darüberhinaus werde Lastgangdaten öffter mit excel geöfnet oder verarbeitet manchmal unbemerkt die Formate ändert.


```python
get_screen_shots('differente_date_formats', 7)
```

Das Parser API kann mehr als 150 unterschiedliche Datumsformate erkennen und korrekt einlesen.


```python
# get all date formats from api
get_accepted_date_formats('01.01.2020 01:00')
```

### 2.2. Leerzeichen, leehre Spalten und Zeilen korrigieren

Beipiele:


```python
get_screen_shots('white spaces', 4)
```

### 2.3. Kleine menschlichen Fehlern korrigieren

Beipiele:


```python
get_screen_shots('human_errors', 1)
```

## 3. Lastgang-Zeitreihe überprüfen und bei Bedarf Korrigieren und ergänzen

### 3.1. Zeitsempel nach Konsistenz überprüfen

Der Lastgang muss einen kontinuierlichen und eindeutigen Zeitstempel haben. Der Zeitstempel-Interval muss über den gesamten Zeitraum konsistenz sein (täglich, stündlich, viertelstündlich).

Bei Inkonsitenz gibt es drei Szenarien:

Szenario 1: Format-bezogene Inkonsitenzen werden automatisch behoben


```python
filepath = "webinar/csv/6.csv"
df = pd.read_csv(filepath, header=None, delimiter=';')
df
```


```python
parse(path = filepath, get_meta = False)
```

Szenario 2: die Inkonsistenz im Zeitstempel ist kein Format-fehler und der die Option "force read" ist auf "False" gesetzt.

In diesem fall wir ein Fehler ausgegeben.


```python
filepath = "webinar/csv/7.csv"
df = pd.read_csv(filepath, header=None, delimiter=';')
df.head(15)
```


```python
parse(path = filepath, fix_time_stamps= False, get_meta = False)
```

Szenario 3: die Inkonsistenz im Zeitstempel ist kein Format-fehler und der die Option "force read" ist auf "True" gesetzt.

In diesem fall wir zeitstempel korrigiert/ersetzt.


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False)
```

### 3. 2. Zeitreihe nach Vollständigkeit überprüfen

#### 3. 2. 1. Zeitstempel

Die Messwerte im Lastgang dürfen nicht unterbrochen sein. 


```python
filepath = "webinar/csv/not continious.csv"
df = pd.read_csv(filepath,  delimiter=';')
df
```


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False)
```

#### 3. 2. 1. Werte

Es muss für jeden Zeitstempel einen Messwert geben. Bei fehlende werte kann der benutzer zwischen folgende Optionen auswählen.


```python
filepath = "webinar/csv/missing_values.csv"
df = pd.read_csv(filepath, delimiter=';')
df.head(25)
```

Wir zeigen die zeitreihe in einem Grafik


```python
df['datetime'] = pd.to_datetime(df['datetime'])
df['value'] = df['value'].str.replace(
            '.', '').str.replace(',', '.').astype(float)
df.plot(x='datetime')
```

Jetzt füllen wir die Werte mit vier unterschiedliche methoden:

Alle leere Werte mit 0 füllen


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False, fill_missing_values='0', plot=True)
```

Mit dem vorherigen Wert


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False, fill_missing_values='fill_values_forward', plot=True)
```

Mit dem nächten Wert


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False, fill_missing_values='fill_values_backward', plot=True)
```

Interpolieren


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False, fill_missing_values='interpolate', plot=True)
```

### 3. 3. Sommer-/ Winterzeitumstellung überprüfen und ggf. korrigieren

* Winterzeit: Im letzten Sonntag in Oktober wird die Uhr um 3:00 auf 2:00 zurückgestellt.
* Sommerzeit: Im letzten Sonntag in März wird der Uhr um 2:00 auf 3:00 vorgestellt.
<img src="webinar/pics/zeitumstellung.jpg" width="500px" style="margin-left:0px;"/>

#### 3. 3. 1. Beginn- und Ende-Konvention

Beispiel1: Stündliche Zeitreihen

<tr><td><img width="250px" src="webinar/pics/dls/h_begin.png"/></td><td><img width="250px" src="webinar/pics/dls/h_end.png"/></td></tr>

Beispiel2: Viertelstündliche Zeitreihen

<tr><td><img width="250px" src="webinar/pics/dls/q_begin.png"/></td><td><img width="250px" src="webinar/pics/dls/q_end.png"/></td></tr>

#### 3. 3. 1. Beispiel für Korrektur der Zeitumstellung


```python
filepath = "webinar/csv/sum_begin_q .csv"
df = pd.read_csv(filepath, header=None, delimiter=';')
df.head(15)
```


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False)
```


```python
filepath = "webinar/csv/sum_end_q.csv"
df = pd.read_csv(filepath, header=None, delimiter=';')
df.head(15)
```


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False)
```

<br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>


```python
import json
import requests
import urllib.parse
import pandas as pd
import numpy as np
import datetime
import locale
from IPython.core.display import display, HTML
from dateutil import parser as date_parser
import copy
    
def get_screen_shots(fold, n):
    htm = "<tr>"
    for i in range(1,n+1):
        htm += "<td><img width='250px' src='webinar/pics/{}/{}.png'/></td>".format(fold, i)
        if i % 3 ==0:
            htm += "</tr><tr>"
    display(HTML(htm))
    
    
def get_accepted_date_formats(date_str):
    function = 'accepted-date-formats'
    url = api_endpoint + '/' + function

    #call
    rslt = requests.get(url)

    #check if http error 
    if rslt.status_code != 200:
        print (rslt.status_code, rslt.reason, rslt.text)

    else:
        #get date fromat from response
        formats = json.loads(json.loads(rslt.text))
        arr_formats = np.array(formats)
        
        # set Locale to germany
        locale.setlocale(locale.LC_TIME, "de_DE")

        # format now in all variations
        dt = date_parser.parse(date_str)
        for f in arr_formats:
            print(dt.strftime(f))
```
