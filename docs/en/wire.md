title: Resistance of a conductor

<div class="w3-row-padding" style="padding-left: 0px;">
  <div class="w3-quarter">
    <label for="materials">Material</label>
    <select class="w3-select w3-border w3-theme-l1" name="materials" id="materials" onChange="calcWireResistance();"></select>
  </div>
  <div class="w3-quarter">
    <label for="temperature">Temperature (°C)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="temperature" id="temperature" type="number" min="0" max="100" value="20" onChange="calcWireResistance();">
  </div>
  <div class="w3-quarter">
    <label for="wire-length">Length (m)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="wire-length" id="wire-length" type="number" min="0.01" value="10" onChange="calcWireResistance();">
  </div>
  <div class="w3-quarter">
    <label for="cross-section">Cross section (mm²)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="cross-section" id="cross-section" type="number" min="0.1" value="1.5" onChange="calcWireResistance();">
  </div>
</div>

<div class="w3-row-padding" style="padding-left: 0px;">
  <div class="w3-quarter">
    <label for="wire-load">Load type</label>
    <select class="w3-select w3-border w3-theme-l1" name="wire-load" id="wire-load" onChange="changeLoad();">
      <option value="1">single wire</option>
      <option value="2">DC</option>
      <option value="2" selected>one-phase AC</option>
      <option value="1.732">three-phase AC</option>
    </select>
  </div>
  <div class="w3-quarter">
    <label for="wire-resist">Resistance (Ohm)</label>
    <pre style="margin-top: 0px !important; margin-bottom: 0px !important;"><code id="wire-resist" name="wire-resist"></code></pre>
  </div>
</div>

<div class="w3-row-padding" style="padding-left: 0px;">
  <div class="w3-quarter">
    <label for="current">Current (A)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="current" id="current" type="number" min="0" value="12.1" onChange="calcPower();">
  </div>
  <div class="w3-quarter">
    <label for="voltage">Voltage (V)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="voltage" id="voltage" type="number" min="0" value="230" onChange="calcPower();">
  </div>
  <div class="w3-quarter">
    <label for="power">Power (kW)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="power" id="power" type="number" min="0" value="2.5" onChange="calcCurrent();">
  </div>
  <div class="w3-quarter" id="cosphi-input">
    <label for="cosphi">cos(phi)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" name="cosphi" id="cosphi" type="number" min="0.8" max="1" value="0.9" onChange="calcCurrent();">
  </div>
</div>

<div class="w3-row-padding" style="padding-left: 0px;">
  <div class="w3-quarter">
    <label for="wire-voltage-drop">Voltage drop (V)</label>
    <pre style="margin-top: 0px !important; margin-bottom: 0px !important;"><code id="wire-voltage-drop" name="wire-voltage-drop"></code></pre>
  </div>
  <div class="w3-quarter">
    <label for="wire-vpercent-drop">Voltage drop (%)</label>
    <select class="w3-select w3-border w3-theme-l1" name="wire-vpercent-drop" id="wire-vpercent-drop" onclick="calculateCrossSection();">
      <option value="0" disabled selected>-</option>
      <option value="0.5">0.5</option>
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="3">3</option>
    </select>
  </div>
  <div class="w3-quarter">
    <label for="wire-losses">Losses (W)</label>
    <pre style="margin-top: 0px !important; margin-bottom: 0px !important;"><code id="wire-losses" name="wire-losses"></code></pre>
  </div>
  <div class="w3-quarter">
    <label for="wire-losses-perm">Losses (W/m)</label>
    <pre style="margin-top: 0px !important; margin-bottom: 0px !important;"><code id="wire-losses-perm" name="wire-losses-perm"></code></pre>
  </div>
</div>

All information is given without warranty.
{: .w3-panel .w3-theme-l3 .w3-leftbar .w3-rightbar .w3-border-orange }

## Why should the voltage drop be low and not exceed 3%?

Devices will probably malfunction if the voltage drop is too high after active load is applied on the wire.
An additional problem arises with AC, because reactive currents gain an increasingly negative effect on energy transmission, if the wire was undersized.

/// html | div.w3-cell-row
//// html | div.w3-cell.w3-container.w3-mobile
![](wire/cosphi10ud03.svg)
////
//// html | div.w3-cell.w3-container.w3-mobile
![](wire/cosphi05ud03.svg)
////
///

If the voltage drop is small, then a consumer will see 223V instead if 230V - that's what all devices should be able to handle.
In the first plot only active power is transmitted and approx. 70W is lost on the wire respectively converted to heat (that's the consumption of a light bulb!)
In the second image the current is twice as high due to reactive current.
The reactive current has no notable influence on the voltage drop, but he causes losses that are four times higher.

/// html | div.w3-cell-row
//// html | div.w3-cell.w3-container.w3-mobile
![](wire/cosphi10ud15.svg)
////
//// html | div.w3-cell.w3-container.w3-mobile
![](wire/cosphi05ud11.svg)
////
///

If the wire was dimmed too low, then the voltage drop from the grid to the consumer isn't easy to calculate.
At the same active power level drawn from the grid the voltage drop can vary (even be lower) depending on the rective current.
If the wire is undersized and carries reactive current it could even lead from way less to no power reaching the consumer.

<script>
var materials = [
  {"name": "Copper", "roh20": "0.0178", "alpha": "3.9"},
  {"name": "Aluminium", "roh20": "0.0287", "alpha": "3.8"},
  {"name": "Iron", "roh20": "0.10", "alpha": "6.1"},
  {"name": "Gold", "roh20": "0.022", "alpha": "3.9"}
];
</script>
<script type="text/javascript" src="../../wire/wire.js"></script>
