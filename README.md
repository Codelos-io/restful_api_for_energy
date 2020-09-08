# Bearbeitung von Lastgangdaten mit Codelos.io RESTful APIs

## 1. Einführung

   ### 1. 1. Codelos.io Open-APIs für Lastgangmanagement

Codelos.io bietet eine Reihe von Funktionalitäten in der Energiewirtschaft für Entwickler. Diese ermöglichen es, dass Entwickler sich auf die Kernfunktionalitäten ihrer Produkte fokussieren können um dadurch eine Menge Zeit und Geld zu sparen.

Weitere Infos über die RESTful APIs von Codelos.io finden Sie unter:<br>
https://developer.codelos.io

Die Dokumentation zu diesen APIs finden Sie unter:<br>
https://www.developer.codelos.io/#/docu

Hier finden Sie Code-Beispiele für die folgenden Programmiersprachen:
* [Python](https://developper.codelos.io/#/docu)
* [PHP](https://developper.codelos.io/#/docu)

Weitere nützliche Inhalte für Entwickler finden Sie unter:<br>
https://github.com/Codelos-io

   ### 1. 2.  Authentifizierung

Authentifizierung nach oAuth Methode mit Benutzername und Passwort:


```python
# API end point
api_endpoint = localhost
function = 'oauth/token'

# credencials
params = '?username={}&password={}'.format(urllib.parse.quote('louqmane@gharnati.com'), urllib.parse.quote(psswrd))

# call oauth
url = api_endpoint + '/' + function + params 
response = requests.get(url )

#get Token form response
token = json.loads(response.text)
```

Die Funktion ```oauth/token``` gibt einen Bearer Token zurück der in den weiteren Aufrufen der Funktion verwendet wird.


```python
# token
token = json.loads(response.text)
token
```




    'eyJraWQiOiI5M2NHeEY5dHhINTl2SWJpVDR1NDZQQ0VvOFNGZ2xEdno5THg1UVRcL2k3Yz0iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJjOWRmNTUzMC0zOGQ3LTQzMTEtODM5Zi00OThmNTMzZDU0ZmYiLCJldmVudF9pZCI6ImYzNTFhMzMwLWY4ZDAtNDFlMy05YmM5LWJlNDE2Zjc4NGQ3ZiIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1OTk1OTAwODEsImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC5ldS1jZW50cmFsLTEuYW1hem9uYXdzLmNvbVwvZXUtY2VudHJhbC0xX25lNjBzOURReSIsImV4cCI6MTU5OTU5MzY4MSwiaWF0IjoxNTk5NTkwMDgxLCJqdGkiOiJlY2JmMGUyOS02ZmY0LTQyNjktODIxYy1mM2MxYjQyNmE2NjIiLCJjbGllbnRfaWQiOiIzcnNqdGJ0c2YyYnY1ZjE4N2Q5OTg5ZDJzbCIsInVzZXJuYW1lIjoiYzlkZjU1MzAtMzhkNy00MzExLTgzOWYtNDk4ZjUzM2Q1NGZmIn0.FBj1JlkVBODneLpVabks5T-YXtgUmN41G4A2HKTo8ze1o5JMNgnZC2VczuPyCUMPOaqdeHaz-gUwT5yeOMOZhECIf7F9J307T8wBt-BaYoe3xUO62o5eeJeJdw77c6sTk3xigCvOaTtSb7HJ87sAPnGMWCI3lHSfg8F0Seuc9BcgZmogk4ueOFV4KDILgDmRlQ5KKHj_-F_bqeN4O58XRcvVpLJntLGQlgzcy34aTTt-pUmsRIfBhrh20wpJSi9NMaRdqJ9fg2ivGZHtiMXGk6Lh9jhk2EgDw1hFHrx5xYpO4WXm0Yja6RWNoWG6IUWiooZyjAxPy-XgppluuqC7gg'



   ### 1. 3.  Loslegen mit dem ersten Aufruf

In dem folgenden Code-Beispiel werden wir mit der Funktion ```parse``` der API einen Lastgang aus einer csv-Datei einlesen und als JSON zurückgeben.


```python
# parse load curve function
function = 'parse'
url = api_endpoint + '/' + function

# get file
filepath= 'assets/csv/get_started.csv'
with open(filepath) as fp:
    file_data = fp.read()
    file = {'file': ('file.csv', file_data,'text/csv')}

#call
rslt = requests.post(url = url, data ={'token' : token, 'response_format' : 'json', 'file': 'file.csv',}, files = file )
```

JSON response in einem Dataframe einlesen und ausgeben.


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




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>datetime</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2012-01-01 01:00:00</td>
      <td>396.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2012-01-02 01:00:00</td>
      <td>474.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2012-01-03 01:00:00</td>
      <td>463.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2012-01-04 01:00:00</td>
      <td>492.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2012-01-05 01:00:00</td>
      <td>511.0</td>
    </tr>
    <tr>
      <td>5</td>
      <td>2012-01-06 01:00:00</td>
      <td>509.0</td>
    </tr>
    <tr>
      <td>6</td>
      <td>2012-01-07 01:00:00</td>
      <td>487.0</td>
    </tr>
    <tr>
      <td>7</td>
      <td>2012-01-08 01:00:00</td>
      <td>482.0</td>
    </tr>
    <tr>
      <td>8</td>
      <td>2012-01-09 01:00:00</td>
      <td>523.0</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2012-01-10 01:00:00</td>
      <td>535.0</td>
    </tr>
    <tr>
      <td>10</td>
      <td>2012-01-11 01:00:00</td>
      <td>501.0</td>
    </tr>
    <tr>
      <td>11</td>
      <td>2012-01-12 01:00:00</td>
      <td>499.0</td>
    </tr>
    <tr>
      <td>12</td>
      <td>2012-01-13 01:00:00</td>
      <td>558.0</td>
    </tr>
    <tr>
      <td>13</td>
      <td>2012-01-14 01:00:00</td>
      <td>500.0</td>
    </tr>
    <tr>
      <td>14</td>
      <td>2012-01-15 01:00:00</td>
      <td>524.0</td>
    </tr>
  </tbody>
</table>
</div>



Wir packen den Code in einer Funktion und fügen weitere Optionen für die nächsten Beispiele in der Dokumentation.


```python
def parse(path, fix_time_stamps= True, get_meta = True, fill_missing_values='no_fill', plot=False, translate='no_translation'):
    # parse load curve function
    function = 'parse'
    url = api_endpoint + '/' + function

    # get file
    with open(path) as fp:
        file_data = fp.read()
        file = {'file': ('file.csv', file_data,'text/csv')}

    #call
    rslt = requests.post(url = url, data ={'token' : token, 'file': 'file.csv', 'force_read' : fix_time_stamps, 'fill_missing_values': fill_missing_values, 'translate_error_messages': translate}, files = file )

    #check http error responses
    if rslt.status_code != 200:
        print (rslt.status_code, rslt.reason, rslt.text)
    else:
        if get_meta:
            # read meta data
            meta_data = json.loads(json.loads(rslt.text))
            del meta_data[0]['data'] 
            meta_data[0]['begin']=pd.to_datetime(meta_data[0]['begin'],unit='ms').strftime('%d.%m.%Y %H:%M')
            meta_data[0]['end']=pd.to_datetime(meta_data[0]['end'],unit='ms').strftime('%d.%m.%Y %H:%M')
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
            display(df.head(30))
```

## 2. Lastgangzeitreihen erkennen und einlesen

### 2. 1. Zeitreihe lokalisieren

Beispiel:
Der Lastgang unten ist ein typisches Beispiel einer Datei, indem Werte nicht direkt in der ersten Zeile/Spalte zu finden sind.

<img width="800px" style="margin-left:0" src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/localize/1.png" />

Die API-Funktion ```/parse``` sucht den ersten Zeitstempel in der Datei, überprüft ob es sich um eine Zeitreihe handelt und liest dann die Werte.

Darüber hinaus berechnet die Funktion die Kennzahlen des Lastgangs. 


```python
filepath ='assets/csv/localize.csv'
parse(filepath)
```

    {
        "avr": 219.69032287597656,
        "begin": "01.01.2012 07:00",
        "end": "03.01.2012 17:00",
        "freq": "H",
        "max": 553.344970703125,
        "name": "file",
        "sum": 12961.7294921875,
        "unit": "kWh"
    }



<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>datetime</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2012-01-01 07:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2012-01-01 08:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2012-01-01 09:00:00</td>
      <td>29.556000</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2012-01-01 10:00:00</td>
      <td>49.353001</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2012-01-01 11:00:00</td>
      <td>39.504002</td>
    </tr>
    <tr>
      <td>5</td>
      <td>2012-01-01 12:00:00</td>
      <td>405.403992</td>
    </tr>
    <tr>
      <td>6</td>
      <td>2012-01-01 13:00:00</td>
      <td>158.332001</td>
    </tr>
    <tr>
      <td>7</td>
      <td>2012-01-01 14:00:00</td>
      <td>79.163002</td>
    </tr>
    <tr>
      <td>8</td>
      <td>2012-01-01 15:00:00</td>
      <td>78.986000</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2012-01-01 16:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>10</td>
      <td>2012-01-01 17:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>11</td>
      <td>2012-01-01 18:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>12</td>
      <td>2012-01-01 19:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>13</td>
      <td>2012-01-01 20:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>14</td>
      <td>2012-01-01 21:00:00</td>
      <td>39.594002</td>
    </tr>
    <tr>
      <td>15</td>
      <td>2012-01-01 22:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>16</td>
      <td>2012-01-01 23:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>17</td>
      <td>2012-01-02 00:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>18</td>
      <td>2012-01-02 01:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>19</td>
      <td>2012-01-02 02:00:00</td>
      <td>49.348999</td>
    </tr>
    <tr>
      <td>20</td>
      <td>2012-01-02 03:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>21</td>
      <td>2012-01-02 04:00:00</td>
      <td>69.009003</td>
    </tr>
    <tr>
      <td>22</td>
      <td>2012-01-02 05:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>23</td>
      <td>2012-01-02 06:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>24</td>
      <td>2012-01-02 07:00:00</td>
      <td>493.279999</td>
    </tr>
    <tr>
      <td>25</td>
      <td>2012-01-02 08:00:00</td>
      <td>236.865005</td>
    </tr>
    <tr>
      <td>26</td>
      <td>2012-01-02 09:00:00</td>
      <td>177.229996</td>
    </tr>
    <tr>
      <td>27</td>
      <td>2012-01-02 10:00:00</td>
      <td>354.118988</td>
    </tr>
    <tr>
      <td>28</td>
      <td>2012-01-02 11:00:00</td>
      <td>522.000977</td>
    </tr>
    <tr>
      <td>29</td>
      <td>2012-01-02 12:00:00</td>
      <td>493.144989</td>
    </tr>
  </tbody>
</table>
</div>


### 2. 2. Unterschiedliche Datums- und Zeitformate erkennen und einlesen

In den folgenden Beispiele sieht man unterschiedliche Zeitstempelformate für Lastgänge. In der Praxis tauchen sehr viele Formate auf. Dies sind immer von der Herkunft der Daten abhängig.

Unterschiedliche EDMs nutzen unterschiedliche Exportformate. Daten die direkt aus einer Datenbank exportiert wurden, können wiederum auch andere Formate haben.

Darüber hinaus werden Lastgangdaten mit Excel geöffnet oder verarbeitet und können sogar manchmal unbemerkt die Formate ändern.


```python
get_screen_shots('differente_date_formats', 7)
```


<table><tr><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/differente_date_formats/1.png" width="250px"/></td><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/differente_date_formats/2.png" width="250px"/></td><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/differente_date_formats/3.png" width="250px"/></td></tr><tr><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/differente_date_formats/4.png" width="250px"/></td><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/differente_date_formats/5.png" width="250px"/></td><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/differente_date_formats/6.png" width="250px"/></td></tr><tr><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/differente_date_formats/7.png" width="250px"/></td></tr></table>


Die Parser-API kann mehr als 150 unterschiedliche Datumsformate erkennen und korrekt einlesen.


```python
# get all date formats from api
get_accepted_date_formats('01.01.2020 01:00')
```

    01.01.2020
    01/01/2020
    01/01/2020
    2020-01-01
    01. Januar 2020
    01. Jan 2020
    01.01.20
    01/01/20
    01/01/20
    20-01-01
    Mi 01.01.2020
    Mi 01/01/2020
    Mi 01/01/2020
    Mi 2020-01-01
    Mi 01. Januar 2020
    Mi 01. Jan 2020
    Mi 01.01.20
    Mi 01/01/20
    Mi 01/01/20
    Mi 20-01-01
    Mi, 01.01.2020
    Mi, 01/01/2020
    Mi, 01/01/2020
    Mi, 2020-01-01
    Mi, 01. Januar 2020
    Mi, 01. Jan 2020
    Mi, 01.01.20
    Mi, 01/01/20
    Mi, 01/01/20
    Mi, 20-01-01
    Mittwoch 01.01.2020
    Mittwoch 01/01/2020
    Mittwoch 01/01/2020
    Mittwoch 2020-01-01
    Mittwoch 01. Januar 2020
    Mittwoch 01. Jan 2020
    Mittwoch 01.01.20
    Mittwoch 01/01/20
    Mittwoch 01/01/20
    Mittwoch 20-01-01
    Mittwoch, 01.01.2020
    Mittwoch, 01/01/2020
    Mittwoch, 01/01/2020
    Mittwoch, 2020-01-01
    Mittwoch, 01. Januar 2020
    Mittwoch, 01. Jan 2020
    Mittwoch, 01.01.20
    Mittwoch, 01/01/20
    Mittwoch, 01/01/20
    Mittwoch, 20-01-01
    01.01.2020 01:00:00
    01.01.2020 01:00
    01/01/2020 01:00:00
    01/01/2020 01:00
    01/01/2020 01:00:00
    01/01/2020 01:00
    2020-01-01 01:00:00
    2020-01-01 01:00
    01. Januar 2020 01:00:00
    01. Januar 2020 01:00
    01. Jan 2020 01:00:00
    01. Jan 2020 01:00
    01.01.20 01:00:00
    01.01.20 01:00
    01/01/20 01:00:00
    01/01/20 01:00
    01/01/20 01:00:00
    01/01/20 01:00
    20-01-01 01:00:00
    20-01-01 01:00
    Mi 01.01.2020 01:00:00
    Mi 01.01.2020 01:00
    Mi 01/01/2020 01:00:00
    Mi 01/01/2020 01:00
    Mi 01/01/2020 01:00:00
    Mi 01/01/2020 01:00
    Mi 2020-01-01 01:00:00
    Mi 2020-01-01 01:00
    Mi 01. Januar 2020 01:00:00
    Mi 01. Januar 2020 01:00
    Mi 01. Jan 2020 01:00:00
    Mi 01. Jan 2020 01:00
    Mi 01.01.20 01:00:00
    Mi 01.01.20 01:00
    Mi 01/01/20 01:00:00
    Mi 01/01/20 01:00
    Mi 01/01/20 01:00:00
    Mi 01/01/20 01:00
    Mi 20-01-01 01:00:00
    Mi 20-01-01 01:00
    Mi, 01.01.2020 01:00:00
    Mi, 01.01.2020 01:00
    Mi, 01/01/2020 01:00:00
    Mi, 01/01/2020 01:00
    Mi, 01/01/2020 01:00:00
    Mi, 01/01/2020 01:00
    Mi, 2020-01-01 01:00:00
    Mi, 2020-01-01 01:00
    Mi, 01. Januar 2020 01:00:00
    Mi, 01. Januar 2020 01:00
    Mi, 01. Jan 2020 01:00:00
    Mi, 01. Jan 2020 01:00
    Mi, 01.01.20 01:00:00
    Mi, 01.01.20 01:00
    Mi, 01/01/20 01:00:00
    Mi, 01/01/20 01:00
    Mi, 01/01/20 01:00:00
    Mi, 01/01/20 01:00
    Mi, 20-01-01 01:00:00
    Mi, 20-01-01 01:00
    Mittwoch 01.01.2020 01:00:00
    Mittwoch 01.01.2020 01:00
    Mittwoch 01/01/2020 01:00:00
    Mittwoch 01/01/2020 01:00
    Mittwoch 01/01/2020 01:00:00
    Mittwoch 01/01/2020 01:00
    Mittwoch 2020-01-01 01:00:00
    Mittwoch 2020-01-01 01:00
    Mittwoch 01. Januar 2020 01:00:00
    Mittwoch 01. Januar 2020 01:00
    Mittwoch 01. Jan 2020 01:00:00
    Mittwoch 01. Jan 2020 01:00
    Mittwoch 01.01.20 01:00:00
    Mittwoch 01.01.20 01:00
    Mittwoch 01/01/20 01:00:00
    Mittwoch 01/01/20 01:00
    Mittwoch 01/01/20 01:00:00
    Mittwoch 01/01/20 01:00
    Mittwoch 20-01-01 01:00:00
    Mittwoch 20-01-01 01:00
    Mittwoch, 01.01.2020 01:00:00
    Mittwoch, 01.01.2020 01:00
    Mittwoch, 01/01/2020 01:00:00
    Mittwoch, 01/01/2020 01:00
    Mittwoch, 01/01/2020 01:00:00
    Mittwoch, 01/01/2020 01:00
    Mittwoch, 2020-01-01 01:00:00
    Mittwoch, 2020-01-01 01:00
    Mittwoch, 01. Januar 2020 01:00:00
    Mittwoch, 01. Januar 2020 01:00
    Mittwoch, 01. Jan 2020 01:00:00
    Mittwoch, 01. Jan 2020 01:00
    Mittwoch, 01.01.20 01:00:00
    Mittwoch, 01.01.20 01:00
    Mittwoch, 01/01/20 01:00:00
    Mittwoch, 01/01/20 01:00
    Mittwoch, 01/01/20 01:00:00
    Mittwoch, 01/01/20 01:00
    Mittwoch, 20-01-01 01:00:00
    Mittwoch, 20-01-01 01:00


### 2.2. Leerzeichen, leere Spalten und Zeilen korrigieren

Beipiele:


```python
get_screen_shots('white spaces', 4)
```


<table><tr><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/white spaces/1.png" width="250px"/></td><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/white spaces/2.png" width="250px"/></td><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/white spaces/3.png" width="250px"/></td></tr><tr><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/white spaces/4.png" width="250px"/></td></tr></table>


### 2.3. Kleine menschliche Fehler korrigieren

Beipiele:


```python
get_screen_shots('human_errors', 1)
```


<table><tr><td style="border: none;background: white;"><img src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/human_errors/1.png" width="250px"/></td></tr></table>


## 3. Lastgangzeitreihe überprüfen und bei Bedarf korrigieren und ergänzen

### 3.1. Zeitstempel nach Konsistenz überprüfen

Der Lastgang muss einen kontinuierlichen und eindeutigen Zeitstempel haben. Der Zeitstempel-Intervall muss über den gesamten Zeitraum konsistenz sein (täglich, stündlich, viertelstündlich).

Bei Inkonsistenz gibt es drei Szenarien:

Szenario 1: Formatbezogene Inkonsistenzen werden automatisch behoben


```python
filepath = "assets/csv/6.csv"
df = pd.read_csv(filepath, header=None, delimiter=';')
df
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>28.10.19 01:00</td>
      <td>13,14</td>
    </tr>
    <tr>
      <td>1</td>
      <td>28.10.19 01:15</td>
      <td>11,7</td>
    </tr>
    <tr>
      <td>2</td>
      <td>28.10.19 01:30</td>
      <td>12,26</td>
    </tr>
    <tr>
      <td>3</td>
      <td>28.10.19 01:45</td>
      <td>13,04</td>
    </tr>
    <tr>
      <td>4</td>
      <td>28.10.19 02:00</td>
      <td>12,6</td>
    </tr>
    <tr>
      <td>5</td>
      <td>28.10.19 02:15</td>
      <td>12,2</td>
    </tr>
    <tr>
      <td>6</td>
      <td>28.10.19 02:30</td>
      <td>12,96</td>
    </tr>
    <tr>
      <td>7</td>
      <td>28.10.19 02:45</td>
      <td>19,08</td>
    </tr>
    <tr>
      <td>8</td>
      <td>28.10.19 03:00</td>
      <td>16,22</td>
    </tr>
    <tr>
      <td>9</td>
      <td>28.10.19 02:15 s</td>
      <td>13,38</td>
    </tr>
    <tr>
      <td>10</td>
      <td>28.10.2019 02:30:00 s</td>
      <td>13,88</td>
    </tr>
    <tr>
      <td>11</td>
      <td>28.10.2019 02:45:00 s</td>
      <td>13,94</td>
    </tr>
    <tr>
      <td>12</td>
      <td>28.10.2019 03:00:00 s</td>
      <td>12,36</td>
    </tr>
    <tr>
      <td>13</td>
      <td>28.10.19 03:15</td>
      <td>11,36</td>
    </tr>
    <tr>
      <td>14</td>
      <td>28.10.19 03:30</td>
      <td>11,52</td>
    </tr>
    <tr>
      <td>15</td>
      <td>28.10.19 03:45</td>
      <td>11,84</td>
    </tr>
    <tr>
      <td>16</td>
      <td>28.10.19 04:00</td>
      <td>11,5</td>
    </tr>
    <tr>
      <td>17</td>
      <td>28.10.19 04:15</td>
      <td>11,28</td>
    </tr>
    <tr>
      <td>18</td>
      <td>28.10.19 04:30</td>
      <td>14,42</td>
    </tr>
  </tbody>
</table>
</div>




```python
parse(path = filepath, get_meta = False)
```


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>datetime</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2019-10-28 01:00:00</td>
      <td>13.140000</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2019-10-28 01:15:00</td>
      <td>11.700000</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2019-10-28 01:30:00</td>
      <td>12.260000</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2019-10-28 01:45:00</td>
      <td>13.040000</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2019-10-28 02:00:00</td>
      <td>12.600000</td>
    </tr>
    <tr>
      <td>5</td>
      <td>2019-10-28 02:15:00</td>
      <td>12.200000</td>
    </tr>
    <tr>
      <td>6</td>
      <td>2019-10-28 02:30:00</td>
      <td>12.960000</td>
    </tr>
    <tr>
      <td>7</td>
      <td>2019-10-28 02:45:00</td>
      <td>19.080000</td>
    </tr>
    <tr>
      <td>8</td>
      <td>2019-10-28 03:00:00</td>
      <td>16.219999</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2019-10-28 03:15:00</td>
      <td>13.380000</td>
    </tr>
    <tr>
      <td>10</td>
      <td>2019-10-28 03:30:00</td>
      <td>13.880000</td>
    </tr>
    <tr>
      <td>11</td>
      <td>2019-10-28 03:45:00</td>
      <td>13.940000</td>
    </tr>
    <tr>
      <td>12</td>
      <td>2019-10-28 04:00:00</td>
      <td>12.360000</td>
    </tr>
    <tr>
      <td>13</td>
      <td>2019-10-28 04:15:00</td>
      <td>11.360000</td>
    </tr>
    <tr>
      <td>14</td>
      <td>2019-10-28 04:30:00</td>
      <td>11.520000</td>
    </tr>
    <tr>
      <td>15</td>
      <td>2019-10-28 04:45:00</td>
      <td>11.840000</td>
    </tr>
    <tr>
      <td>16</td>
      <td>2019-10-28 05:00:00</td>
      <td>11.500000</td>
    </tr>
    <tr>
      <td>17</td>
      <td>2019-10-28 05:15:00</td>
      <td>11.280000</td>
    </tr>
  </tbody>
</table>
</div>


Szenario 2: Die Inkonsistenz im Zeitstempel ist kein Formatfehler und die Option "force read" ist auf "False" gesetzt.

In diesem Fall wird ein Fehler ausgegeben.


```python
filepath = "assets/csv/7.csv"
df = pd.read_csv(filepath, header=None, delimiter=';')
df.head(15)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>28.10.19 01:00</td>
      <td>13,14</td>
    </tr>
    <tr>
      <td>1</td>
      <td>28.10.19 01:15</td>
      <td>11,7</td>
    </tr>
    <tr>
      <td>2</td>
      <td>28.10.19 01:30</td>
      <td>12,26</td>
    </tr>
    <tr>
      <td>3</td>
      <td>28.10.19 01:45</td>
      <td>13,04</td>
    </tr>
    <tr>
      <td>4</td>
      <td>28.10.19 02:00</td>
      <td>12,6</td>
    </tr>
    <tr>
      <td>5</td>
      <td>28.10.19 02:15</td>
      <td>12,2</td>
    </tr>
    <tr>
      <td>6</td>
      <td>28.10.19 02:30</td>
      <td>12,96</td>
    </tr>
    <tr>
      <td>7</td>
      <td>28.10.19 02:45</td>
      <td>19,08</td>
    </tr>
    <tr>
      <td>8</td>
      <td>28.10.19 03:00</td>
      <td>16,22</td>
    </tr>
    <tr>
      <td>9</td>
      <td>28.10.19</td>
      <td>11,36</td>
    </tr>
    <tr>
      <td>10</td>
      <td>28.10.19</td>
      <td>11,52</td>
    </tr>
    <tr>
      <td>11</td>
      <td>28.10.19</td>
      <td>11,84</td>
    </tr>
    <tr>
      <td>12</td>
      <td>28.10.19 04:00</td>
      <td>11,5</td>
    </tr>
    <tr>
      <td>13</td>
      <td>28.10.19 04:15</td>
      <td>11,28</td>
    </tr>
    <tr>
      <td>14</td>
      <td>28.10.19 04:30</td>
      <td>14,42</td>
    </tr>
  </tbody>
</table>
</div>




```python
parse(path = filepath, fix_time_stamps= False, get_meta = False)
```

    400 BAD REQUEST {"message": "Unexpected time stamps in rows:  '9', '10', '11'. Expected time stamps are:  '28.10.19 03:15', '28.10.19 03:30', '28.10.19 03:45'."}
    


Szenario 3: Die Inkonsistenz im Zeitstempel ist kein Formatfehler und die Option "force read" ist auf "True" gesetzt.

In diesem Fall wird der Zeitstempel korrigiert/ersetzt.


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False)
```


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>datetime</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2019-10-28 01:00:00</td>
      <td>13.140000</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2019-10-28 01:15:00</td>
      <td>11.700000</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2019-10-28 01:30:00</td>
      <td>12.260000</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2019-10-28 01:45:00</td>
      <td>13.040000</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2019-10-28 02:00:00</td>
      <td>12.600000</td>
    </tr>
    <tr>
      <td>5</td>
      <td>2019-10-28 02:15:00</td>
      <td>12.200000</td>
    </tr>
    <tr>
      <td>6</td>
      <td>2019-10-28 02:30:00</td>
      <td>12.960000</td>
    </tr>
    <tr>
      <td>7</td>
      <td>2019-10-28 02:45:00</td>
      <td>19.080000</td>
    </tr>
    <tr>
      <td>8</td>
      <td>2019-10-28 03:00:00</td>
      <td>16.219999</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2019-10-28 03:15:00</td>
      <td>11.360000</td>
    </tr>
    <tr>
      <td>10</td>
      <td>2019-10-28 03:30:00</td>
      <td>11.520000</td>
    </tr>
    <tr>
      <td>11</td>
      <td>2019-10-28 03:45:00</td>
      <td>11.840000</td>
    </tr>
    <tr>
      <td>12</td>
      <td>2019-10-28 04:00:00</td>
      <td>11.500000</td>
    </tr>
    <tr>
      <td>13</td>
      <td>2019-10-28 04:15:00</td>
      <td>11.280000</td>
    </tr>
  </tbody>
</table>
</div>


### 3. 2. Zeitreihe nach Vollständigkeit überprüfen

#### 3. 2. 1. Zeitstempel

Die Messwerte im Lastgang dürfen nicht unterbrochen sein. 


```python
filepath = "assets/csv/not continious.csv"
df = pd.read_csv(filepath,  delimiter=';')
df
```


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False)
```

#### 3. 2. 1. Werte

Es muss für jeden Zeitstempel einen Messwert geben. Wenn Werte fehlen, kann der Benutzer zwischen folgende Optionen auswählen.


```python
filepath = "assets/csv/missing_values.csv"
df = pd.read_csv(filepath, delimiter=';')
df.head(25)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>datetime</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>01.01.19 01:00</td>
      <td>8,137260922</td>
    </tr>
    <tr>
      <td>1</td>
      <td>01.01.19 02:00</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>2</td>
      <td>01.01.19 03:00</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>3</td>
      <td>01.01.19 04:00</td>
      <td>6,378645852</td>
    </tr>
    <tr>
      <td>4</td>
      <td>01.01.19 05:00</td>
      <td>5,919927712</td>
    </tr>
    <tr>
      <td>5</td>
      <td>01.01.19 06:00</td>
      <td>5,643054359</td>
    </tr>
    <tr>
      <td>6</td>
      <td>01.01.19 07:00</td>
      <td>5,565623675</td>
    </tr>
    <tr>
      <td>7</td>
      <td>01.01.19 08:00</td>
      <td>5,538640255</td>
    </tr>
    <tr>
      <td>8</td>
      <td>01.01.19 09:00</td>
      <td>5,697021199</td>
    </tr>
    <tr>
      <td>9</td>
      <td>01.01.19 10:00</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>10</td>
      <td>01.01.19 11:00</td>
      <td>6,285963671</td>
    </tr>
    <tr>
      <td>11</td>
      <td>01.01.19 12:00</td>
      <td>6,694234547</td>
    </tr>
    <tr>
      <td>12</td>
      <td>01.01.19 13:00</td>
      <td>6,864347412</td>
    </tr>
    <tr>
      <td>13</td>
      <td>01.01.19 14:00</td>
      <td>6,91714106</td>
    </tr>
    <tr>
      <td>14</td>
      <td>01.01.19 15:00</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>15</td>
      <td>01.01.19 16:00</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>16</td>
      <td>01.01.19 17:00</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>17</td>
      <td>01.01.19 18:00</td>
      <td>2,861415713</td>
    </tr>
    <tr>
      <td>18</td>
      <td>01.01.19 19:00</td>
      <td>2,558732132</td>
    </tr>
    <tr>
      <td>19</td>
      <td>01.01.19 20:00</td>
      <td>2,717113076</td>
    </tr>
    <tr>
      <td>20</td>
      <td>01.01.19 21:00</td>
      <td>2,835605486</td>
    </tr>
    <tr>
      <td>21</td>
      <td>01.01.19 22:00</td>
      <td>3,254435092</td>
    </tr>
  </tbody>
</table>
</div>



Wir zeigen die Zeitreihe in einer Grafik.


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False, fill_missing_values='no_fill', plot=True)
```


![png](output_34_0.png)


Jetzt füllen wir die Werte mit vier unterschiedlichen Methoden:

Alle leere Werte mit 0 füllen.


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False, fill_missing_values='0', plot=True)
```


![png](output_36_0.png)


Mit dem vorherigen Wert füllen.


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False, fill_missing_values='fill_values_forward', plot=True)
```


![png](output_38_0.png)


Mit dem nächsten Wert füllen.


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False, fill_missing_values='fill_values_backward', plot=True)
```


![png](output_40_0.png)


Interpolieren.


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False, fill_missing_values='interpolate', plot=True)
```


![png](output_42_0.png)


### 3. 3. Sommer-/ Winterzeitumstellung überprüfen und ggf. korrigieren

* Winterzeit: Im letzten Sonntag im Oktober wird die Uhr von 3:00 auf 2:00 zurückgestellt.
* Sommerzeit: Im letzten Sonntag im März wird die Uhr von 2:00 auf 3:00 vorgestellt.
<img src="assets/pics/zeitumstellung.jpg" width="500px" style="margin-left:0px;"/>

#### 3. 3. 1. Beginn- und Ende-Konvention

Beispiel 1: Stündliche Zeitreihen:

<br>
<table style="margin-left:0px">
    <tr>
        <td><img width="250px" src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/dls/h_begin.png"/></td>
        <td><img width="250px" src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/dls/h_end.png"/></td>
    </tr>
</table>
<br>

Beispiel 2: Viertelstündliche Zeitreihen:

<br>
<table style="margin-left:0px"><tr><td><img width="250px" src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/dls/q_begin.png"/></td><td><img width="250px" src="https://codelos-public-media.s3.eu-central-1.amazonaws.com/assets/pics/dls/q_end.png"/></td></tr></table>
<br>

#### 3. 3. 1. Beispiel für Korrektur der Zeitumstellung


```python
filepath = "assets/csv/sum_begin_q .csv"
df = pd.read_csv(filepath, header=None, delimiter=';')
df.head(15)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>So 31.03.19  00:00:00</td>
      <td>22,33</td>
    </tr>
    <tr>
      <td>1</td>
      <td>So 31.03.19  00:15:00</td>
      <td>23,59</td>
    </tr>
    <tr>
      <td>2</td>
      <td>So 31.03.19  00:30:00</td>
      <td>0,32</td>
    </tr>
    <tr>
      <td>3</td>
      <td>So 31.03.19  00:45:00</td>
      <td>22,27</td>
    </tr>
    <tr>
      <td>4</td>
      <td>So 31.03.19  01:00:00</td>
      <td>21,33</td>
    </tr>
    <tr>
      <td>5</td>
      <td>So 31.03.19  01:15:00</td>
      <td>22,55</td>
    </tr>
    <tr>
      <td>6</td>
      <td>So 31.03.19  01:30:00</td>
      <td>21,95</td>
    </tr>
    <tr>
      <td>7</td>
      <td>So 31.03.19  01:45:00</td>
      <td>20,69</td>
    </tr>
    <tr>
      <td>8</td>
      <td>So 31.03.19  02:00:00</td>
      <td>0</td>
    </tr>
    <tr>
      <td>9</td>
      <td>So 31.03.19  02:15:00</td>
      <td>0</td>
    </tr>
    <tr>
      <td>10</td>
      <td>So 31.03.19  02:30:00</td>
      <td>0</td>
    </tr>
    <tr>
      <td>11</td>
      <td>So 31.03.19  02:45:00</td>
      <td>0</td>
    </tr>
    <tr>
      <td>12</td>
      <td>So 31.03.19  03:00:00</td>
      <td>20,75</td>
    </tr>
    <tr>
      <td>13</td>
      <td>So 31.03.19  03:15:00</td>
      <td>20,81</td>
    </tr>
    <tr>
      <td>14</td>
      <td>So 31.03.19  03:30:00</td>
      <td>20,45</td>
    </tr>
  </tbody>
</table>
</div>




```python
parse(path = filepath, fix_time_stamps= True, get_meta = False)
```


```python
filepath = "assets/csv/sum_end_q.csv"
df = pd.read_csv(filepath, header=None, delimiter=';')
df.head(15)
```


```python
parse(path = filepath, fix_time_stamps= True, get_meta = False)
```


```python
df.tail()
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>15</td>
      <td>So 31.03.19  03:45:00</td>
      <td>21,17</td>
    </tr>
    <tr>
      <td>16</td>
      <td>So 31.03.19  04:00:00</td>
      <td>21,45</td>
    </tr>
    <tr>
      <td>17</td>
      <td>So 31.03.19  04:15:00</td>
      <td>21,83</td>
    </tr>
    <tr>
      <td>18</td>
      <td>So 31.03.19  04:30:00</td>
      <td>21,67</td>
    </tr>
    <tr>
      <td>19</td>
      <td>So 31.03.19  04:45:00</td>
      <td>21,83</td>
    </tr>
  </tbody>
</table>
</div>
