title: Wie lautet meine IP Adresse?

<p>&nbsp;</p>
<p>
  <strong class="w3-large">IPv6:</strong> 
  <code class="w3-large w3-responsive" id="ip6" onclick="copyToClipboard('ip6');" style="cursor: copy;">lädt...</code>
</p>
<p>
  <strong class="w3-large">IPv4:</strong> 
  <code class="w3-large w3-responsive" id="ip4" onclick="copyToClipboard('ip4');" style="cursor: copy;">lädt...</code>
</p>
<div>
  <strong>Standort:</strong>
  <span id="iploc"></span>
</div>
<div class="w3-small">
  <strong>Anbieter:</strong>
  <span id="ipasn"></span>
</div>


*inspiriert von:* Weil alle anderen "What's my IP?" Seiten Mist sind. [Host yourself :3](https://git.clerie.de/clerie/ip.clerie.de){ target="_blank" }
{: .w3-small }

Dieser Dienst verwendet die GeoLite2 Datenbank von MaxMind, erhältlich unter [https://www.maxmind.com](https://www.maxmind.com){ target="_blank" }
{: .w3-small }

## Woher kennt es meine IP Adresse?

Wenn man im Internet surft wird über 
[DNS Abfragen](https://de.wikipedia.org/wiki/Domain_Name_System){ target="_blank" } der Name einer Webseite in eine
[IP Adresse](https://de.wikipedia.org/wiki/IP-Adresse){ target="_blank" } umgewandelt.
Diese Adresse ist notwendig um eine Verbindung zu dem Webserver der Seite herzustellen, sie kann den
Internetzugang aber auch eindeutig identifizieren.

Dabei fragt der Browser einen DNS Server nach der Adresse und bekommt als Antwort entweder eine
[IPv4](https://de.wikipedia.org/wiki/IPv4){ target="_blank" } oder [IPv6](https://de.wikipedia.org/wiki/IPv6){ target="_blank" }
oder beides (dann entscheidet der Browser welches Protokoll er bevorzugt).

Ich habe [wie-lautet-ip4.unverbuggt.de](https://wie-lautet-ip4.unverbuggt.de){ target="_blank" } so eingestellt,
dass nur eine IPv4 zurückgeliefert wird. Deshalb stellt der Browser die Verbindung auch rein über IPv4 her. 
Der Server kennt die IP des Clients sobald eine Verbindung mit ihm aufgebaut wird.
Bei [wie-lautet-ip6.unverbuggt.de](https://wie-lautet-ip6.unverbuggt.de){ target="_blank" } 
wird dagegen die Verbindung rein auf IPv6 gezwungen.

Es kann sein, dass nur eine der beiden Felder eine IP Adresse anzeigt. Wenn z.B. alte Betriebssysteme oder Netzwerkgeräte für 
den Internetzugang verwendet werden funktioniert u. U. nur IPv4 und bei einigen Mobilfunkzugängen kann es sein, dass nur IPv6
funktioniert oder dieselbe IPv4 für mehrere Nutzer verwendet wird. 

## Wie kann es wissen wo ich ungefähr bin?

Zuallererst: Deine Daten werden hier mit keinem Drittanbieter geteilt.

Auf der Server läuft eine Datenbank die den Ort und
Providerdaten in Abhängigkeit von IP Adressbereichen enthält. Solche Datenbanken kann man von verschiedenen Anbietern als
kostenlose oder als Bezahlversion (mit angeblich erhöhter Genauigkeit) beziehen. Diese Informationen werden normalerweise
für [Geoblocking](https://de.wikipedia.org/wiki/Geoblocking){ target="_blank" } verwendet oder um lokale Werbung anzuzeigen.

## Was kann ich dagegen tun?

Nutze einfach [tor](https://www.torproject.org/de/download/){ target="_blank" }.

Alle Angaben ohne Gewähr.
{: .w3-panel .w3-theme-l3 .w3-leftbar .w3-rightbar .w3-border-orange }

<script>
var lang = "de";
var sErr404 = "Fehler: Interner Fehler.";
var sErr = "Fehler: Konnte IP nicht abrufen.";

function copyToClipboard(id) {
  let ip = document.getElementById(id);
  navigator.clipboard.writeText(ip.textContent);
}

function GetLangString(dict) {
  if (lang in dict) {
    return dict[lang];
  }
  else if ("en" in dict) {
    return dict["en"];
  }
  else {
    return dict[0];
  }
}

var gettingLoc = false;
function getLoc(ip_id) {
  if (gettingLoc) {
    return;
  }
  gettingLoc = true;
  let loc = document.getElementById("iploc");
  fetch('https://wie-lautet-' + ip_id + '.unverbuggt.de/?city')
  .then(
    function(response) {
      if (response.status == 200) {
        response.json().then(
          function(data) {
            let locstr = "";
            if ("continent" in data) {
              locstr += GetLangString(data["continent"]["names"])
            }
            if ("country" in data) {
              locstr += " / " + GetLangString(data["country"]["names"])
            }
            if ("subdivisions" in data) {
              locstr += " / " + GetLangString(data["subdivisions"][0]["names"])
            }
            if ("city" in data) {
              locstr += " / " + GetLangString(data["city"]["names"])
            }
            loc.textContent = locstr;
          }
        );
      }
    }
  );
}

var gettingAsn = false;
function getAsn(ip_id) {
  if (gettingAsn) {
    return;
  }
  gettingAsn = true;
  let asn = document.getElementById("ipasn");
  fetch('https://wie-lautet-' + ip_id + '.unverbuggt.de/?asn')
  .then(
    function(response) {
      if (response.status == 200) {
        response.json().then(
          function(data) {
            asn.textContent = data["autonomous_system_organization"];
          }
        );
      }
    }
  );
}

function getIp(ip_id) {
  let ip = document.getElementById(ip_id);
  fetch('https://wie-lautet-' + ip_id + '.unverbuggt.de/')
  .then(
    function(response) {
      if (response.status !== 200) {
        ip.textContent = sErr404;
        return;
      }
      response.text().then(function(text) {
        ip.textContent = text;
        getLoc(ip_id);
        getAsn(ip_id);
      });
    }
  )
  .catch(
    function(err) {
      ip.textContent = sErr;
    }
  );
}

getIp("ip4");
getIp("ip6");
</script>
