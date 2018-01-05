# MMM-EFA-departures
MagicMirror² module to show departures for public transport stations using the EFA system.

**Example Configuration for Düsseldorf HBF/Main Station:**
```
{
    module: 'MMM-EFA-departures',
    position: 'top_right',
    config: {
        efaUrl: "http://efa.vrr.de/standard/XSLT_DM_REQUEST",
        stopID: "20018235",                                //stopID offered by the provider (Düsseldorf HBF in this case)
        stopName: "MMM-EFA is loading",                    //initial module name
        lines: ['all'],                                    //lines: ['DDB:92E01: :H','DDB:92E01: :R'], would only show the line S1 in both directions
        reload: 60000,                                     //interval in ms (60000=60s)
        realDepTime: true,                                 //use real-time data
        relativeDepTime: true,                             // When "toggle" is disabled change between absolute/relative Time,not implemented yet
        toggleDepTime: true,                              //Toggle relative/absolute time
        toggleDepTimePerReload: 6,                         //Every 10 seconds
        fade: true,                                        //fade brightness
        fadePoint: 0.25,                                   //Start on 1/4th of the list. (1/maxDepartures would be ideal)
        maxDepartures: 4                                   //maximum amount of departures displayed
    }
},
```

**Some API-Endpoints (EfaUrl):**
```
VRR / Verkehrsverbund Rhein-Ruhr
http://efa.vrr.de/standard/XSLT_DM_REQUEST

VVS / Verkehrs- und Tarifverbund Stuttgart
http://www2.vvs.de/vvs/XSLT_DM_REQUEST

MVV / Münchner Verkehrs- und Tarifverbund
http://efa.mvv-muenchen.de/mobile/XSLT_DM_REQUEST

DING - Donau-Iller-Nahverkehrsverbund
http://www.ding.eu/mobile/ding2/XSLT_DM_REQUEST

BW - Baden_Württemberg 
http://www.efa-bw.de/nvbw/XSLT_DM_REQUEST?

traveline south east: Journey Planner
http://www.travelinesoutheast.org.uk/se/XSLT_DM_REQUEST
```

**Getting Station and Line IDs**  
you can get the information by viewing the source (in Chrome for instance) of the EFA-Page  
**view-source:http://efa.vrr.de/standard/XSLT_DM_REQUEST**  
I extracted a few lines and stations (most of them for efa.vrr.de)  
[Stations](https://github.com/Dom1n1c/MMM-EFA-departures/files/999639/stations.txt)  
[Lines](https://github.com/Dom1n1c/MMM-EFA-departures/files/999640/lines.txt)  
using a script from this [blog](http://www.eg-blog.de/?p=822)  
```
#!/bin/bash
i=$1
j=$2
until [ $i -gt $j ]
do
	wget http://efa.vrr.de/standard/XSLT_DM_REQUEST --no-verbose --post-data "language=de&name_dm=$i&type_dm=stop&mode=direct&dmLineSelectionAll=1&depType=STOPEVENTS&includeCompleteStopSeq=1&useRealtime=1&limit=8&itdLPxx_hideNavigationBar=false&itdLPxx_transpCompany=Refresh&timeOffset=0"
	grep 'Abfahrten ab:' XSLT_DM_REQUEST >> temp
	if [ -s temp ]; then
		echo -n $i >> Liste.txt
		echo $i
		echo -n " " >> Liste.txt
		grep 'Abfahrten ab:' XSLT_DM_REQUEST | sed -e "s/^\s*<td colspan="3">//" -e "s/<\/td>\s*$//" | cut -b32- >>Liste.txt
		rm temp
	fi
	rm XSLT_DM_REQUEST
	i=$(( $i + 1 ))
done
```
