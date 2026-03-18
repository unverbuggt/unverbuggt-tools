title: Wann geht die Sonne auf?

Dieses Tool enthält Code aus [oscat](http://oscat.de/){ target="_blank" } (übersetzt in Javascript).
{: .w3-container .w3-small style="padding-left: 8px;"}

<div class="w3-row-padding"  style="padding-left: 0px;">
  <div class="w3-third">
    <label for="latitude">Breitengrad (° / S- / N+)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" type="number" name="latitude" id="latitude" onchange="getOSMLink();">
  </div>
  <div class="w3-third">
    <label for="longitude">Längengrad (° / W- / O+)</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" type="number" name="longitude" id="longitude" onchange="getOSMLink();">
  </div>
  <div class="w3-third">
    <label id="status-location">&nbsp;</label>
    <div id="get-location"></div>
  </div>
</div>

<div class="w3-row-padding w3-margin-top" style="padding-left: 0px;">
  <div class="w3-half">
    <label for="osm" id="osm-link"><a href="https://www.openstreetmap.org" target="_blank">OpenStreetMap Link</a></label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" type="text" name="osm" id="osm" onclick="this.value='';" onchange="parseOSMLink();">
  </div>
</div>

<div class="w3-row-padding w3-margin-top" style="padding-left: 0px;">
  <div class="w3-third">
    <label for="day">Tag</label>
    <input class="w3-input w3-border w3-hover-theme w3-theme-l1" type="date" id="day" name="day" onchange="calcSun();">
  </div>
</div>

<div class="w3-row-padding w3-margin-top" style="padding-left: 0px;">
  <div class="w3-third" id="sun-output">&nbsp;</div>
  <div class="w3-third" id="sun-output-utc">&nbsp;</div>
  <div class="w3-third" id="sun-day-length">&nbsp;</div>
</div>

Alle Angaben ohne Gewähr.
{: .w3-panel .w3-theme-l3 .w3-leftbar .w3-rightbar .w3-border-orange }

<script>
var sGetLocation = "Standort ermitteln";
var sGettingLocation = "Suche Standort...";
var sError = "Fehler: ";
var sErrUnknown = "Unbekannter Fehler.";
var sErrPermission = "Zugriff verweigert.";
var sErrNoPosition = "Position nicht verfügbar.";
var sErrTimeout = "Zeitüberschreitung. (Am besten vorher feste Position ermitteln)";
var sOSMLink = "OpenStreetMap Link";
var sCouldntCalc = "Konnte nicht berechnet werden. Bitte Eingabe prüfen.";
var sSunrise = "Sonnenaufgang: ";
var sSunset = "Sonnenuntergang: ";
var sMidday = "Mittag: ";
var sDayLength = "Tageslänge: ";
var sTwilight = "Dämmerung: ";
var sDeclination = "Deklination: ";
var sLocal = "Ortszeit des Systems";
var sUTC = "UTC Zeitzone";
var sMisc = "Verschiedenes";
var sErrLati = "Es kann nur zwischen 65° nördlicher oder südlicher Breite berechnet werden.";
var sErrInvDate = "Ungültiger Tag.";

//
var latitude = document.getElementById('latitude');
var longitude = document.getElementById('longitude');
var osm = document.getElementById('osm');
var get_location = document.getElementById('get-location');
var day = document.getElementById('day');
var osm_link = document.getElementById('osm-link');
var sun_output = document.getElementById('sun-output');
var sun_output_utc = document.getElementById('sun-output-utc');
var sun_day_length = document.getElementById('sun-day-length');


var tomorrow = new Date(Date.now()+86400000);
tomorrow.setUTCHours(0,0,0,0);
//#sun=47.0/12.0/1970-01-01
if (window.location.hash && window.location.hash.startsWith('#sun=')) {
  let values = window.location.hash.substr(5).split("/");
  if (values.length >= 2 && !isNaN(values[0]) && !isNaN(values[1])) {
    latitude.value = values[0];
    longitude.value = values[1];
  }
  if (values.length == 3) {
    day.value = values[2];
  }
}
if (day.value == '') { //set to next day if unset
  day.value = tomorrow.toISOString().substring(0,10);
}

// check for Geolocation support
if (navigator.geolocation) {
  get_location.innerHTML = '<button class="w3-button w3-theme-l1 w3-hover-theme" onclick="getLocation();">' + sGetLocation + '</button>';
}


getOSMLink();

function getLocation() {
  let status_location = document.getElementById('status-location');
  
  var startPos;
  var geoOptions = {
    enableHighAccuracy: false,
    timeout: 30000,
    maximumAge: Infinity
  };

  var geoSuccess = function (position) {
    status_location.innerHTML = '&nbsp;';
    startPos = position;
    latitude.value = startPos.coords.latitude;
    longitude.value = startPos.coords.longitude;
    getOSMLink();
  };
  var geoError = function (error) {
    // error.code can be:
    switch(error.code) {
      case 1: //   1: permission denied
        status_location.innerHTML = sError + sErrPermission;
        break;
      case 2: //   2: position unavailable (error response from location provider)
        status_location.innerHTML = sError + sErrNoPosition;
        break;
      case 3: //   3: timed out
        status_location.innerHTML = sError + sErrTimeout;
        break;
      default: //   0: unknown error
        status_location.innerHTML = sError + sErrUnknown;
    }
  };

  navigator.geolocation.getCurrentPosition(geoSuccess, geoError, geoOptions);
  status_location.innerHTML = sGettingLocation;
}

function parseOSMLink() {
  let osmvalues = osm.value.split("/");
  if (osmvalues.length == 6 && !isNaN(osmvalues[4]) && !isNaN(osmvalues[5])) {
    latitude.value = osmvalues[4];
    longitude.value = osmvalues[5];
  }
  getOSMLink();
}

var gettingOSMLink = false;
function getOSMLink() {
  if (gettingOSMLink) {
    return;
  }
  gettingOSMLink = true;
  
  let date = new Date(day.value);
  
  if (latitude.value != '' && longitude.value != '') {
    osm.value = 'https://www.openstreetmap.org/#map=14/' + latitude.value + '/' + longitude.value;
    osm_link.innerHTML = '<a href="' + osm.value + '" target="_blank">' + sOSMLink + '</a>';
    calcSun();
  } else {
    osm_link.innerHTML = '<a href="https://www.openstreetmap.org" target="_blank">' + sOSMLink + '</a>';
    sun_output.innerHTML = sCouldntCalc;
    sun_output_utc.innerHTML = '&nbsp;';
    sun_day_length.innerHTML = '&nbsp;';
  }
  
  gettingOSMLink = false;
}

function calcSun() {
  let date = new Date(day.value);
  if (latitude.value > 65 || latitude.value < -65) {
    sun_output.innerHTML = sErrLati;
    sun_output_utc.innerHTML = '&nbsp;';
    sun_day_length.innerHTML = '&nbsp;';
  } else if (date instanceof Date && !isNaN(date.valueOf())) {
    sunTime(latitude.value, longitude.value, date);
  } else {
    sun_output.innerHTML = sErrInvDate;
    sun_output_utc.innerHTML = '&nbsp;';
    sun_day_length.innerHTML = '&nbsp;';
  }
}

function getLang()
{
  if (navigator.languages != undefined) {
    return navigator.languages[0]; 
  } else {
    return navigator.language;
  }
}

function copyToClipboard(id) {
  let ip = document.getElementById(id);
  navigator.clipboard.writeText(ip.textContent);
}

//https://stackoverflow.com/questions/8619879/javascript-calculate-the-day-of-the-year-1-366
function daysIntoYear(date) {
    let now = Date.UTC(date.getFullYear(), date.getMonth(), date.getDate());
    let start = Date.UTC(date.getFullYear(), 0, 0);
    let diff = now - start;
    let oneDay = 1000 * 60 * 60 * 24;
    return Math.ceil(diff / oneDay);
}

function hoursToNewDate(hours, date) {
  var copiedDate = new Date(date.getTime());
  let h = parseInt(hours);
  let m = parseInt(Math.round((hours - h) * 60));
  copiedDate.setUTCHours(h,m,0,0);
  return copiedDate;
}

//originally translated to javascript from http://www.oscat.de/

//this FUNCTION calculates the time when the sun stand exactly south of a given location.
function sunMidday(longi, date) {
  let t = daysIntoYear(date);
  //https://web.archive.org/web/20200511112651/https://lexikon.astronomie.info/zeitgleichung/
  let offset = (-0.171 * Math.sin(0.0337 * t + 0.465 )) - (0.1299 * Math.sin(0.01787 * t - 0.168 ));
  //let offset = (-0.1752 * Math.sin(0.033430 * t + 0.5474)) - (0.1340 * Math.sin(0.018234 * t - 0.1939));
  let tod = 12.0 - offset - (longi / 15);
  return tod;
}

//this FUNCTION block calculates the sun rise, sun set, sun offset at midday sun declination for a given date 
//for performance reasons the algorithm has been simplified and is accurate within a few minutes only 
//the times are calculated in utc and have to be corrected for the given time zone
//this correction is not done within sun_time because it would be a problem on days where dst is enabled or disabled
function sunTime(lati, longi, date) {
  let b = lati * Math.PI / 180;
  let mid_day = sunMidday(longi, date);
  //https://web.archive.org/web/20200511112651/https://lexikon.astronomie.info/zeitgleichung/
  let dk = 0.4095 * Math.sin(0.016906 * (daysIntoYear(date) - 80.086));
  //let dk = 0.40954 * Math.sin(0.0172 * (daysIntoYear(date) - 79.35));
  //let dk = 23.44 * Math.PI / 180 * Math.sin(2 * Math.PI / 365 * (daysIntoYear(date) - 81));
  //let dk = 23.44 * Math.PI / 180 * Math.sin(2 * Math.PI / 365 * (daysIntoYear(date) - 81));
  let sun_declination = dk * 180 / Math.PI;
  if (sun_declination > 180.0) {
    sun_declination = sun_declination - 360.0;
  }
  let delta_end = 12 * Math.acos((Math.sin(-6 * Math.PI / 180) - Math.sin(b) * Math.sin(dk)) / (Math.cos(b) * Math.cos(dk))) / Math.PI;
  //https://web.archive.org/web/20171008103842/http://lexikon.astronomie.info/zeitgleichung/tagbogen.html
  //-0.83 Grad = Sonnenaufgang am mathematischen Horizont mit Refraktion
  let delta_start = 12 * Math.acos((Math.sin(-0.83 * Math.PI / 180) - Math.sin(b) * Math.sin(dk)) / (Math.cos(b) * Math.cos(dk))) / Math.PI;
  let twilight = delta_end - delta_start
  let sun_rise = mid_day - delta_start;
  let sun_set = mid_day + delta_start;
  let day_length = 2 * delta_start;
  let sunrise = hoursToNewDate(sun_rise, date);
  let midday =  hoursToNewDate(mid_day, date);
  let sunset = hoursToNewDate(sun_set, date);
  let options = {hour: 'numeric', minute: 'numeric'};
  let options_utc = {hour: 'numeric', minute: 'numeric', timeZone: 'UTC'};
  let lang = getLang();
  sun_output.innerHTML = '<label>' + sLocal + '</label><br><pre class="w3-code" style="margin-top: 0px !important;"><code id="sunriseset" onclick="copyToClipboard(\'sunriseset\')" style="cursor: copy;">' + sSunrise + sunrise.toLocaleTimeString(lang,options) + '\n' + sMidday + midday.toLocaleTimeString(lang,options) + '\n' + sSunset + sunset.toLocaleTimeString(lang,options) + '</code></pre>';
  sun_output_utc.innerHTML = '<label>' + sUTC + '</label><br><pre class="w3-code" style="margin-top: 0px !important;"><code id="sunrisesetutc" onclick="copyToClipboard(\'sunrisesetutc\')" style="cursor: copy;">' + sSunrise + sunrise.toLocaleTimeString(lang,options_utc) + '\n' + sMidday + midday.toLocaleTimeString(lang,options_utc) + '\n' + sSunset + sunset.toLocaleTimeString(lang,options_utc) + '</code></pre>';
  sun_day_length.innerHTML = '<label>' + sMisc + '</label><br><pre class="w3-code" style="margin-top: 0px !important;"><code id="misc" onclick="copyToClipboard(\'misc\')" style="cursor: copy;">' + sDayLength + day_length.toFixed(1) + 'h\n' + sTwilight + (twilight * 60).toFixed(0) + 'm\n' + sDeclination + sun_declination.toFixed(1) + '°</code></pre>';
  
  if (date.getTime() == tomorrow.getTime()) {
    window.location.hash='#sun=' + lati + '/'+ longi;
  } else {
    window.location.hash='#sun=' + lati + '/'+ longi + '/' + date.toISOString().substring(0,10);
  }
}

</script>
