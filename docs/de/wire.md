title: Widerstand einer Leitung

<div class="w3-row-padding" style="padding-left: 0px;">
  <div class="w3-quarter">
    <label for="materials">Material</label>
    <select class="w3-select w3-border w3-theme-l1" name="materials" id="materials" onChange="calcWireResistance();"></select>
  </div>
  <div class="w3-quarter">
    <label for="temperature">Temperatur (°C)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="temperature" id="temperature" type="number" min="0" max="100" value="20" onChange="calcWireResistance();">
  </div>
  <div class="w3-quarter">
    <label for="wire-length">Länge (m)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="wire-length" id="wire-length" type="number" min="0.01" value="10" onChange="calcWireResistance();">
  </div>
  <div class="w3-quarter">
    <label for="cross-section">Querschnitt (mm²)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="cross-section" id="cross-section" type="number" min="0.1" value="1.5" onChange="calcWireResistance();">
  </div>
</div>

<div class="w3-row-padding" style="padding-left: 0px;">
  <div class="w3-quarter">
    <label for="wire-load">Belastung</label>
    <select class="w3-select w3-border w3-theme-l1" name="wire-load" id="wire-load" onChange="changeLoad();">
      <option value="1">Einzelleiter</option>
      <option value="2">Gleichstrom</option>
      <option value="2" selected>Wechselstrom</option>
      <option value="1.732">Drehstrom</option>
    </select>
  </div>
  <div class="w3-quarter">
    <label for="wire-resist">Widerstand (Ohm)</label>
    <pre style="margin-top: 0px !important; margin-bottom: 0px !important;"><code id="wire-resist" name="wire-resist"></code></pre>
  </div>
</div>

<div class="w3-row-padding" style="padding-left: 0px;">
  <div class="w3-quarter">
    <label for="current">Strom (A)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="current" id="current" type="number" min="0" value="12.1" onChange="calcPower();">
  </div>
  <div class="w3-quarter">
    <label for="voltage">Spannung (V)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="voltage" id="voltage" type="number" min="0" value="230" onChange="calcPower();">
  </div>
  <div class="w3-quarter">
    <label for="power">Leistung (kW)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="power" id="power" type="number" min="0" value="2.5" onChange="calcCurrent();">
  </div>
  <div class="w3-quarter" id="cosphi-input">
    <label for="cosphi">cos(phi)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="cosphi" id="cosphi" type="number" min="0.8" max="1" value="0.9" onChange="calcCurrent();">
  </div>
</div>

<div class="w3-row-padding" style="padding-left: 0px;">
  <div class="w3-quarter">
    <label for="wire-voltage-drop">Spannungsabfall (V)</label>
    <pre style="margin-top: 0px !important; margin-bottom: 0px !important;"><code id="wire-voltage-drop" name="wire-voltage-drop"></code></pre>
  </div>
  <div class="w3-quarter">
    <label for="wire-vpercent-drop">Spannungsabfall (%)</label>
    <select class="w3-select w3-border w3-theme-l1" name="wire-vpercent-drop" id="wire-vpercent-drop" onclick="calculateCrossSection();">
      <option value="0" disabled selected>-</option>
      <option value="0.5">0,5</option>
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="3">3</option>
    </select>
  </div>
  <div class="w3-quarter">
    <label for="wire-losses">Verlust (W)</label>
    <pre style="margin-top: 0px !important; margin-bottom: 0px !important;"><code id="wire-losses" name="wire-losses"></code></pre>
  </div>
  <div class="w3-quarter">
    <label for="wire-losses-perm">Verlust (W/m)</label>
    <pre style="margin-top: 0px !important; margin-bottom: 0px !important;"><code id="wire-losses-perm" name="wire-losses-perm"></code></pre>
  </div>
</div>

Alle Angaben ohne Gewähr.
{: .w3-panel .w3-theme-l3 .w3-leftbar .w3-rightbar .w3-border-orange }

## Warum sollte der Spannungsabfall gering sein bzw. 3% nicht übersteigen?

Geräte funktionieren mglw. nicht mehr richtig, wenn die Spannung zu tief einbricht sobald die Leitung mit Wirkleistung belastet wird.
Bei Wechsel- oder Drehstrom kommt noch hinzu, dass Blindleistung einen zunehmend negativen Einfluss auf die Energieübertragung hat,
wenn die Leitung zu gering dimensioniert wurde.

/// html | div.w3-cell-row
//// html | div.w3-cell.w3-container.w3-mobile
![](wire/cosphi10ud03.svg)
////
//// html | div.w3-cell.w3-container.w3-mobile
![](wire/cosphi05ud03.svg)
////
///

Ist der Spannungsabfall gering, dann sieht der Verbraucher bei Belastung statt 230V nur 223V - damit sollten alle Geräte klarkommen.
In der ersten Grafik wird nur Wirkleistung übertragen und ca. 70W gehen auf der Leitung verloren bzw. werden zu Wärme (Das ist der Verbrauch einer Glühbirne!).
Im zweiten Bild wird durch den Blindstromanteil der Strom verdoppelt.
Der Blindstrom hat in diesem Fall keinen nennenswerten Einfluss auf den Spannungseinbruch.
Er verursacht jedoch viermal so hohe Leitungsverluste.

/// html | div.w3-cell-row
//// html | div.w3-cell.w3-container.w3-mobile
![](wire/cosphi10ud15.svg)
////
//// html | div.w3-cell.w3-container.w3-mobile
![](wire/cosphi05ud11.svg)
////
///

Ist die Leitung zu gering dimensioniert, dann ist der Spannungsabfall vom Netz zum Verbraucher nicht einfach berechenbar.
Bei gleichem Leistungsbezug aus dem Stromnetz kann der Spannungseinbruch beim Verbraucher je nach Blindstromanteil sogar geringer werden.
Bei einer solch unterdimensionierten Leitung kann ein Blindstromanteil dazu führen, dass fast keine Leistung mehr am Verbraucher ankommt.

<script>
var materials = [
  {"name": "Kupfer", "roh20": "0.0178", "alpha": "3.9"},
  {"name": "Aluminium", "roh20": "0.0287", "alpha": "3.8"},
  {"name": "Eisen", "roh20": "0.10", "alpha": "6.1"},
  {"name": "Gold", "roh20": "0.022", "alpha": "3.9"}
];
</script>
<script type="text/javascript" src="../wire/wire.js"></script>
