title: Guter Passwortgenerator

<link rel="stylesheet" type="text/css" href="../dice/slider.css">

<div class="w3-cell-row w3-margin-bottom">
  <div class="w3-cell">
    <select class="w3-select w3-border w3-theme-l1" name="wordlist" id="wordlist-dropdown" onchange="changeWordlist()"></select>
  </div>
  <div class="w3-cell">&nbsp;</div>
  <div class="w3-cell" id="wordlist-info"></div>
</div>
<input id="words-slider" class="slider w3-theme-l4" style="display: none;" type="range" min="2" max="10" onChange="generatePassword();">
<div class="w3-margin-bottom" id="generate-button"></div>
<div class="w3-row">
  <div id="text-password"></div>
  <div class="w3-twothird">
    <div id="result-password"></div>
  </div>
  <div class="w3-third w3-container">
    <div class="w3-small" id="text-dicerolls"></div>
    <div class="w3-small" id="result-dicerolls"></div>
  </div>
</div>

Die erzeugten Passwörter sollten **niemals** genau so kopiert und verwendet werden. Sie wurden lediglich durch einen Pseudozufallsgenerator erzeugt.
{: .w3-panel .w3-theme-l3 .w3-leftbar .w3-rightbar .w3-border-red }

Die Passwörter werden im Browser erzeugt, dieser Webserver bekommt davon nichts mit.
{: .w3-panel .w3-theme-l3 .w3-leftbar .w3-rightbar .w3-border-orange }

Am besten selber [würfeln](https://de.wikipedia.org/wiki/Diceware){ target="_blank" }, Wörter verändern, Groß-/Kleinschreibung anpassen und Sonderzeichen einfügen um die Sicherheit deutlich zu erhöhen.
{: .w3-panel .w3-theme-l3 .w3-leftbar .w3-rightbar .w3-border-green }

## Selber würfeln

<div class="w3-cell-row">
  <div id="dicerolls-input" class="w3-cell" style="width:50%"></div>
  <div class="w3-cell w3-cell-middle">&nbsp;</div>
  <div id="rollsword-output" class="w3-cell w3-cell-middle" style="width:47%"></div>
</div>

## Vergleich mit Zufallszeichen

Bei Kenntnis der Methode und ohne Änderung sind diese Passwörter mindestens so sicher wie:  
`-`{ #lcase } (nur Kleinbuchstaben)  
`-`{ #ulcase } (Klein- und Großbuchstaben)  
`-`{ #ulcasenum } (Klein-/Großbuchstaben und Zahlen)  
`-`{ #ulcasenumspec } (Klein-/Großbuchstaben, Zahlen und Sonderzeichen)  
oder `-`{ #bits } Bits  
**wenn** sie wirklich gewürfelt wären...

Alle Angaben ohne Gewähr.
{: .w3-panel .w3-theme-l3 .w3-leftbar .w3-rightbar .w3-border-orange }

<script>
var lang = "de";
var sSource = "Quelle";
var sErrWordlists404 = "Fehler: Liste mit Wortlisten nicht gefunden.";
var sErrWordlists = "Fehler: konnte Liste mit Wortlisten nicht herunterladen.";
var sErrWordlist404 = "Fehler: Konnte Wortliste nicht finden.";
var sErrWordlist = "Fehler: Konnte Wortliste nicht herunterladen.";
var sGenerate = "Erzeugen";
var sPasswords = "Passwörter:";
var sDiceRolls = "Würfelwürfe:";
var sRollNotFound = "Würfelwürfe konnten nicht in der Wortliste gefunden werden.";

let defaultOption = document.createElement('option');
defaultOption.text = 'Wortliste auswählen';
defaultOption.setAttribute('disabled', 'disabled');

var dicepath = base_url+"/dice/";
</script>

<script type="text/javascript" src="../dice/dice.js"></script>
