---
description: >-
  Dies ist eine Anleitung wie Mycroft in Deutsch eingerichtet werden kann. Diese Seite beinhaltet auch eine Kurzanleitung. Darin wird die Einrichtung einer Sprachausgabe (TTS), eines alternativen Aktivierwortes (Wake Word) und eine alternative Spracherkennung (STT) thematisiert.
---

# Mycroft auf deutsch

## Erster Einstieg

!!!Aktuell ist das paaren von Mycroft noch nicht vollständig in deutsch möglich, daher sollten sie Mycroft bereits unter
"lang": "en-us" verbunden und eingerichtet haben.

Zuerst, [verbinden Sie sich mit ihrem Gerät (z.B. SSH)](https://mycroft.ai/documentation/mark-1/#connecting-to-the-mark-1-via-ssh).

Folgend wird an den Beispielen Google/Mbrola die Sprachausgabe (TTS) auf deutsch gesetzt. Mycroft bietet weitere TTS-Möglichkeiten über [marytts](http://mary.dfki.de/), [responsive_voice](https://responsivevoice.org/), [polly](https://aws.amazon.com/de/polly/), auf die hier jedoch nicht näher eingegangen wird. Zudem arbeitet die deutsche Community aktuell an einer eigenen TTS Stimme auf [tacotron2](https://github.com/thorstenMueller/deep-learning-german-tts) Basis.

## Text-to-Speech (TTS) über Google (Sprachausgabe)


Ändere oder ersetze die eigene Konfiguration `mycroft-config edit user` mit folgendem Inhalt.

**Data:**

 ```javascript
{
  "lang": "de-de",
  "tts": {
    "module": "google",
    "google": {
      "lang": "de"
    }
  }
}

```
_HINWEIS: Sollte sich die Standardausgabe von Mycroft in deutsch melden hilft eventuell das updaten mit `mycroft-pip install --upgrade gtts` oder `mycroft-pip install --upgrade gtts-token`._

## TTS Alternativ mit espeak mbrola (Sprachausgabe)

Für Rasberry kann mbrola hier herunterladen werden mit `wget http://steinerdatenbank.de/software/mbrola3.0.1h_armhf.deb` und `sudo dpkg -i mbrola3.0.1h_armhf.deb`.
Installiere nun espeak mbrola über `sudo apt install espeak espeak-data mbrola mbrola-de7`.

**Data:**

```javascript
{
  "lang": "de-de",
  "tts": {
    "module": "espeak",
    "espeak": {
      "lang": "german-mbrola-7",
      "voice": "german-mbrola-7"
    }
  }
}
```
_HINWEIS: Die Stimme 7 kann auch durch eine andere Stimme ersetzt werden. Dabei sind alle geraden Zahlen männlich und alle ungeraden Stimmen weiblich._

Mycroft sollte nach einem Neustart auf deutsch hören und sprechen. Viele Skills sind bereits übersetzt und können sofort genutzt werden.

Das **Wake Word** (z.b. Hey Mycroft, Christopher, Hey Ezra, Hey Jarvis) wird hierbei unter [Mycroft Home](https://home.mycroft.ai) eingerichtet.

## Ändern des **Wake Words** in ein deutsches Wort (Aktivierungswort)

Mycroft verwendet [Precise](https://mycroft.ai/documentation/precise) und [PocketSphinx](https://github.com/cmusphinx/pocketsphinx) als **Wake Word**-Mechanismus. Das Standard **Wake Word** auf englisch ist `Hey Mycroft` und nutzt Precise und - falls Precise es nicht erkennt - Pocketsphinx.

Diese Anleitung bezieht sich insbesodere auf Pocketsphinx. Weiterführende Informationen zu Precise findet man [hier](https://mycroft-ai.gitbook.io/docs/using-mycroft-ai/customizations/wake-word#precise) (englisch)

Um das **Wake Word** (in ein deutsch klingendes) zu ändern muss das deutsche Wörterbuch und das deutsche akustische Modell herunterladen werden, danach trainiere es mit dem neusten `sphinxtrain`.

Zuerst erstelle ein Verzeichnis für die `pocketsphinx`-Dateien:

`sudo mkdir -p /usr/local/share/pocketsphinx/model/de/de`

### Laden des deutsche Wörterbuchs und Installation

Als nächstes lade das deutsche Wörterbuch aus dem CMUSphinx-Projekt auf SourceForge herunter_ [cmusphinx-voxforge-de.dic](https://sourceforge.net/projects/cmusphinx/files/Acoustic%20and%20Language%20Models/German/cmusphinx-voxforge-de.dic/download).

`wget https://sourceforge.net/projects/cmusphinx/files/Acoustic%20and%20Language%20Models/German/cmusphinx-voxforge-de.dic/download -O cmusphinx-voxforge-de.dic`

Dann installiere das Wörterbuch in das neu erstellte Verzeichnis.

`sudo mv cmusphinx-voxforge-de.dic /usr/local/share/pocketsphinx/model/de/de.dict`

### Laden des akustischen Modells und Installation

Als nächstes lade das _acoustic model_ [cmusphinx-de-ptm-voxforge-5.2.tar.gz](https://sourceforge.net/projects/cmusphinx/files/Acoustic%20and%20Language%20Models/German/cmusphinx-de-ptm-voxforge-5.2.tar.gz/download) aus dem CMUSphinx-Projekt auf SourceForge.

`wget https://sourceforge.net/projects/cmusphinx/files/Acoustic%20and%20Language%20Models/German/cmusphinx-de-ptm-voxforge-5.2.tar.gz/download -O cmusphinx-de-ptm-voxforge-5.2.tar.gz`

Entpacke dann die gezippte Datei in ein temporäres Verzeichnis.

`tar -xf cmusphinx-de-ptm-voxforge-5.2.tar.gz`

Dann kopiere die entpackte Datei in das PocketSphinx-Verzeichnis

```bash
cd cmusphinx-ptm-voxforge-de-r20171217/model_parameters/voxforge.cd_ptm_5000
sudo mv * /usr/local/share/pocketsphinx/model/de/de
cd ..
```

### Laden des deutschen Sprachmodells und Installation

Als nächstes lade das _German language model_ [cmusphinx-voxforge-de.lm.gz](https://sourceforge.net/projects/cmusphinx/files/Acoustic%20and%20Language%20Models/German/cmusphinx-voxforge-de.lm.gz/download) aus dem CMUSphinx-Projekt auf SourceForge.

`wget https://sourceforge.net/projects/cmusphinx/files/Acoustic%20and%20Language%20Models/German/cmusphinx-voxforge-de.lm.gz/download -O cmusphinx-voxforge-de.lm.gz`

Entpacke dann die gezippte Datei in ein temporäres Verzeichnis.

`gzip -d cmusphinx-voxforge-de.lm.gz`

Dann verschieben es in das PocketSphinx-Verzeichnis

`sudo mv cmusphinx-voxforge-de.lm /usr/local/share/pocketsphinx/model/de/de.lm`

### Überprüfen Sie die Dateien

Überprüfe, ob die folgenden Dateien existieren:

```
/usr/local/share/pocketsphinx/model/de
/usr/local/share/pocketsphinx/model/de/de.dict
/usr/local/share/pocketsphinx/model/de/de.lm
/usr/local/share/pocketsphinx/model/de/de
/usr/local/share/pocketsphinx/model/de/de/feat.params
/usr/local/share/pocketsphinx/model/de/de/mdef
/usr/local/share/pocketsphinx/model/de/de/means
/usr/local/share/pocketsphinx/model/de/de/mixture_weights
/usr/local/share/pocketsphinx/model/de/de/noisedict
/usr/local/share/pocketsphinx/model/de/de/README
/usr/local/share/pocketsphinx/model/de/de/sendump
/usr/local/share/pocketsphinx/model/de/de/transition_matrices
/usr/local/share/pocketsphinx/model/de/de/variances
```

### Verlinken/Kopieren Sie Modell und Wörterbuch in die Mycroft-Verzeichnisse

Navigieren Sie dazu zum Mycroft-Sprachenmodellverzeichnis.

Zum Beispiel, auf `mycroft-core` für Linux, navigiere zu

`~/mycroft-core/mycroft/client/speech/recognizer/model`

Es sollten die folgenden Verzeichnise zu sehen sein:

```bash
$ ls -las
total 24
8 drwxrwxr-x 3 kathyreid kathyreid 4096 Nov 24 00:08 .
8 drwxrwxr-x 3 kathyreid kathyreid 4096 Nov 24 00:08 ..
8 drwxrwxr-x 3 kathyreid kathyreid 4096 Nov 24 00:08 en-us
```

_HINWEIS: Beachte, dass es nur einen Modellordner gibt - `en-us` für American English_

Erstelle hier ein neues Verzeichnis für deutsch, und verlinke es anschließend mit dem `pocketsphinx` Verzeichnis.

```bash
mkdir de
ln -s /usr/local/share/pocketsphinx/model/de/de de/hmm
cp /usr/local/share/pocketsphinx/model/de/de.lm de/de.lm
cp /usr/local/share/pocketsphinx/model/de/de.dict de/de.dict
```
_HINWEIS: Der `cp`-Befehl könnte durch `ln`-Befehl ersetzt werden um Speicherplatz zu sparen. Es ist jedoch möglich, Wörter zu `de.dict` hinzuzufügen; `cp` bedeutet, dass die Originaldatei unverändert bleibt und mit einer Kopie gearbeitet wird._

Falls mit `ln` gearbeitet wird, müssen die folgenden Verzeichnisse, Dateien und symbolische Verknüpft werden:

```
~/mycroft/client/speech/recognizer/model/de
~/mycroft-core/client/speech/recognizer/model/de/hmm
~/mycroft-core/client/speech/recognizer/model/de/de.dict
~/mycroft-core/client/speech/recognizer/model/de/de.lm
```

### Ein deutsches **Wake Word** aussuchen (Aktivierungswort)

Wähle ein deutsches **Wake Word** oder Wake Phrase auf deutsch und stelle sicher, dass es in der `cmusphinx-voxforge-de.dic`-Datei des Sprachmodells enthalten ist. Die Erfahrung zeigt, dass die beste Wahl ein einzelnes Wort mit drei oder mehr [Phonemen](https://de.wikipedia.org/wiki/Phonem) ist.

Überprüfen Sie zum Beispiel, ob das Wort 'hallo' existiert:

`egrep '^hallo ' de.dict`

`hallo HH AE L AX UU`

Wenn das gewählte **Wake Word** oder Phrase nicht in der `de.dict` Datei ist, dann wählen Sie entweder ein anderes **Wake Word** oder ändern Sie die `de.dict`Datei, um es hinzuzufügen.

### Konfiguriere Mycroft für die Verwendung eines deutsch ausgesprochenen **Wake Words** (Aktivierungswort)

Ändere oder ersetze die eigene Konfiguration `mycroft-config edit user` mit folgendem Inhalt.

**Data:**

```javascript
...
"lang": "de-de",
"listener": {
  "wake_word": "hallo",
  "phonemes": "HH AE L AX UU",
  "threshold": 1e-10,
  "standup_word": "hallo",
  "standup_phonemes": "HH AE L AX UU",
  "standup_threshold": 1e-10
},
...
```

## Mozilla Deepspeech STT (Spracherkennung)

Mycroft unterstützt auch Deepspeech STT. Hierfür muss ein deutsches Modell auf einem Deepspeech Server einrichten. [Aashish Agarwal](https://github.com/AASHISHAG/deepspeech-german) hat dazu entsprechende [Modell Files](https://drive.google.com/drive/folders/1L7ILB-TMmzL8IDYi_GW8YixAoYWjDMn1) unter Deepspeech V9.0 veröffentlicht.

### Installation

1. Entpacke die heruntergeladenen Ordner und wechsle hinein. `cd release_v0.9.0`
2. Aktiviere venv durch `source ~/mycroft-core/.venv/bin/activate`.
3. Installiere Deepspeech mit `pip install deepspeech==0.9.0` oder `pip install deepspeech-gpu==0.9.0`.
4. Auf Raspberry installiere noch einige Pakete mit `sudo apt-get install gfortran libopenblas-dev liblapack-dev`
5. Installiere Deepspeech Server `pip install deepspeech-server`.

_HINWEIS: Bei der installation von Deepspeech Server auf einem Rasberry PI sollte über 1 GB RAM Verfügbar sein. Sonst entsprechend über Swap erweitern._

### Erstelle eine Server Konfiguration

Erstelle eine Konfiguationsdatei `config.json`.

**Data:**

```javascript
{
  "deepspeech": {
    "model" :"output_graph.pb",
    //"model" :"output_graph.tflite", ### uncomment for Raspberry
    //use absolute path for startupscript
    "scorer" :"kenlm.scorer",
    "beam_width": 1024,
    "lm_alpha": 0.931289039105002,
    "lm_beta": 1.1834137581510284
  },
  "server": {
    "http": {
      "host": "0.0.0.0",
      "port": 8080,
      "request_max_size": 2048576
    }
  },
  "log": {
    "level": [
      { "logger": "deepspeech_server", "level": "DEBUG"}
    ]
  }
}
```
### Start

1. Starte Deepspeech Server `deepspeech-server --config config.json`
2. Erstelle Testdatei `arecord -f S16_LE -r 16000 test.wav`.
3. Sende Testdatei `curl -X POST --data-binary @test.wav http://localhost:8080/stt`

Antwort sollte nun `test ein zwei drei test(.venv) pi@picroft:~/release_v0.9.0$` lauten.

### Start Deepspeech Server wärend dem booten

Füge folgendes in Startup Datei `/etc/rc.local`. 

```
/home/pi/mycroft-core/.venv/bin/deepspeech-server --config /home/pi/release_v0.9.0/config.json
```

_Hinweis in der `config.json` sollten absolute Pfade angegeben werden._

### Konfiguriere Mycroft für die Verwendung von Deepspeech 

Ändere oder ergänze die eigene Konfiguration `mycroft-config edit user` mit folgendem Inhalt.

**Data:**

```javascript
...
"stt": {
  "deepspeech_server": {
     "uri": "http://localhost:8080/stt"
   },
  "module": "deepspeech_server"
}
```

###

Für weitergehende Fragen besuche [den deutschen channel auf Mycroft chat](https://chat.mycroft.ai/community/channels/language-de).
