title: What's my IP Address?

<p>&nbsp;</p>
<p>
  <strong class="w3-large">IPv6:</strong> 
  <code class="w3-large w3-responsive" id="ip6" onclick="copyToClipboard('ip6');" style="cursor: copy;">Loading...</code>
</p>
<p>
  <strong class="w3-large">IPv4:</strong> 
  <code class="w3-large w3-responsive" id="ip4" onclick="copyToClipboard('ip4');" style="cursor: copy;">Loading...</code>
</p>
<div>
  <strong>Location:</strong>
  <span id="iploc"></span>
</div>
<div class="w3-small">
  <strong>ISP:</strong>
  <span id="ipasn"></span>
</div>


*inspired by:* Because any other "Whats my IP?"-tool sucks. [Host yourself :3](https://git.clerie.de/clerie/ip.clerie.de){ target="_blank" }
{: .w3-small }

This product includes GeoLite2 data created by MaxMind, available from [https://www.maxmind.com](https://www.maxmind.com){ target="_blank" }
{: .w3-small }

## How does it know my IP address?

When you surf the Internet the name of a website is resolved to an 
[IP address](https://en.wikipedia.org/wiki/IP_address){ target="_blank" }
via [DNS](https://en.wikipedia.org/wiki/Domain_Name_System){ target="_blank" }.
This address is needed to establish a connection to the web server of the site.
It also explicitly identifies your Internet access.

In doing so the browser asks a DNS Server for the address. The answer will be either an
[IPv4](https://en.wikipedia.org/wiki/IPv4){ target="_blank" } or [IPv6](https://en.wikipedia.org/wiki/IPv6){ target="_blank" }
address or both (so the browser will connect to the website via it's preferred protocol).

I configured [wie-lautet-ip4.unverbuggt.de](https://wie-lautet-ip4.unverbuggt.de){ target="_blank" } to just return
an IPv4 address via DNS request. For this reason the connection will be established solely via IPv4.
The server knows the client's IP when the connection is established.
In contrast at [wie-lautet-ip6.unverbuggt.de](https://wie-lautet-ip6.unverbuggt.de){ target="_blank" }
this applies to IPv6 only.

It could be that only one of the fields will show a number. If you use an old operating system or old devices at your Internet
connection then probably only IPv4 will work and some mobile conneciton will only return IPv6 or rather many clients will
share the same IPv4.

## How does it know my approximate location?

First of all: your data isn't shared with any third party here.

This server runs a database which contains location and
ISP data  according to IP address ranges. You can get them for free or as paid version (with supposedly increased accuracy)
from some vendors. This information is normally used to preset you with more local ads or
[Geo-blocking](https://en.wikipedia.org/wiki/Geo-blocking){ target="_blank" }.

## What can I do about this?

Simply use [tor](https://www.torproject.org/download/){ target="_blank" }.

All information is given without warranty.
{: .w3-panel .w3-theme-l3 .w3-leftbar .w3-rightbar .w3-border-orange }

<script>
var lang = "en";
var sErr404 = "error: internal error.";
var sErr = "error: couldn't retrieve IP.";

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
