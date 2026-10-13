Potensic atom 3 waypoint tervező!

global.json

App menü	Kulcs	Érték a fájlban

End of Flight: Back to Start	finishAction	BACK
End of Flight: Hover	finishAction	HOVER
End of Flight: Land	finishAction	LAND

End of Flight: RTH	finishAction	RETURN

Global Speed (0,5–10 m/s)	speed	szám, pl. 5.0

Signal Lost: RTH (az egyetlen lehetőség)	lostAction	RETURN

Waypoint Altitude: Custom	globalHeightType + globalHeight	0 + 0

Waypoint Altitude: Gbl. SET (10–120 m)	globalHeightType + globalHeight	1 + a magasság méterben

Forward Flight	isOrder	true

Reverse Flight	isOrder	false


JSON 

#	Kulcs	Mit állít	Látott értékek
1	action	A pontnál végrehajtott művelet	NONE, START_RECORD, STOP_RECORD, PHOTO
2	gimbalPitch	A gimbal dőlése fokban	mindenhol 0
3	gimbalType	Honnan jön a dőlés	DEFINE (a gimbalPitch értéke), POI (a megfigyelt pontra néz)
4	height	Magasság méterben	10.0, 20.0, 50.0
5	hoverTime	Lebegés a pontnál másodpercben	0, 10
6	lat	Szélesség	valós koordináta
7	lng	Hosszúság	valós koordináta
8	poiHeight	A megfigyelt pont magassága	50.0 (alap, ha nincs POI), 35.0
9	poiLat	A megfigyelt pont szélessége	0.0 (nincs), vagy a POI koordinátája
10	poiLng	A megfigyelt pont hosszúsága	0.0 (nincs), vagy a POI koordinátája
11	poiType	Van-e POI	0 (nincs), 1 (van)
12	speed	A pont sebessége m/s-ban	3.5, 5.0
13	speedType	Melyik sebességet használja	GLOBAL (a globális), DEFINE (a saját)
14	yaw	A drón orrának iránya fokban	75, egyébként 0
15	yawType	Honnan jön az irány	TO_WAYPOINT (a következő pont felé), DEFINE (a yaw értéke), TO_POI (a megfigyelt pont felé)
16	zoomRatio	Zoom szorzó	1.0, 2.0, 4.0
17	zoomType	Ki állítja a zoomot	HAND (kézi), DEFINE (a küldetés állítja)
