# Networking for Web Developers   -   KONSPEKT

## From Ping to HTTP

Ping on käsk testimiseks, kas arvuti saab võrguliiklust saata ja vastu võtta ühelt aadressilt teisele. Kogu interneti liiklus on jagatud sõnumiteks, mida nimetatakse pakkideks. Pakk on lühike sõnum, mis saadetakse ühest arvutist teise kasutades nende arvutite aadresse. Ping-käsku saates võtab vastuvõtja operatsioonisüsteem selle vastu ja saadab vastuse tagasi.

Netcat on vahend, et ühendada erinevaid servereid ning käituda ise serverina. NC ühendub pordiga ja saadab sõne (*string*)  (mis näeb välja nagu HTTP päring) veebiserverisse ning seejärel server vastab.

Võrguprotokollid koosnevad mitmest kihist. Ülemine kiht on rakenduskiht (*application layer*), näiteks HTTP on rakendusprotokoll. See kiht hõlmab URL, paroole, veebilehti jne. Rakendusprotokollid põhinevad transportkihiga protokollidel (nt TCP), mis pakuvad pordinumbreid. Transportkihi protokollid põhinevad internetikihil, millega kaasnevad IP-aadressid. IP-d töötavad erineva riistvara peal (nt wifi). Iga kiht sõltub temast vahetult allpool asuvast kihist. Mõned võrguprotokollid ei sobitu kirjeldatud mudelisse, kuid kõik protokollid sõltuvad IP-st.

Programm, mis kuulab TCP-porti, ootab, et teine programm ühenduks sellesse porti. Kui see juhtub, siis kaks programmi saavad saata andmeid edasi-tagasi. Pordinumbrid eristavad  sama peremeesarvuti (*host*) erinevaid rakendusi ja sessioone. Serveril on kindlad pordid eri rakenduste jaoks, näiteks port 80 HTTP jaoks. Klient (*client*) algatab ühenduse ning kliendipool ühendub suvalise pordiga enda pool. Seejärel saab operatsioonisüsteem eristada sissetulevat liiklust vaadates selle aadresse ja pordinumbreid. Enamus süsteemides on pordid 0-1023 reserveeritud programmidele, mis käivituvad vaid administraatori poolt. Tavaliselt saab ainult üks programm kuulata määratud porti korraga. Aga kui programm käivitub, siis see saab jooksutada lõimesid (*run threads*) või alamprotsesse, mis käsitlevad sissetulevaid ühendusi pordis. Teise võimalusena saab programm käsitleda tsüklis (*loop*) mitme ühenduse vahel kõiki korraga. Server käitub sedasi, et olla võimeline käsitlema mitut ühendust korraga. Netcat ei käitu nagu server - see on pigem kuulaja, mis ainult võtab vastu ühte ühendust korraga.

Brauserid ja serverid tegelevad mitme päringuga korraga. Kui brauser laeb serverist veebilehte, siis see peab laadima palju eri faile (HTML, pildid, CSS jne). Mida rohkem brauserid/serverid seda korraga suudavad teha, seda kiiremini kuvatakse veebileht ekraanile. Selleks on mitu võimalust, kuidas programmid saavad hallata mitut ühendust korraga. Üks lihtsamaid ja vanemaid on järgmine: kui tekib uus ühendus, siis server käsib op-süsteemil jagada see kaheks. Teine võimalus kehtib uuemate serverite kohta: serverid tekitavad protsessidest või lõimedest ühise kogumi, millest igaüks saab hallata üht ühendust korraga. See on kiirem võrreldes uue ühenduse loomiseks, kuid selle miinuseks on see, et peale teatud arvu päringute haldamist hakkab süsteem aeglustuma. Kolmas võimalus on järgmine: üksainus protsess vahetab kiiresti päringute haldamist, kui need vabanevad.


## Names and Addresses

IP-aadressi kasutatakse selleks, et määrata kindel sihtkoht internetis, enamasti kindel arvuti. Iga sõnum, mis internetis liigub (nt e-mail, ping), jagatakse pakkidesse ning igal pakil on selle arvuti IP-aadress, kust sõnum tuli ja kuhu läheb.

*Host* on arvuti internetis. Ühenduse lõpp-punktid on kaks arvutit või programmi, mis suhtlevad üle ühenduse.

Käsku "ping" ```ping eesti.ee``` kasutades peab see kõigepealt üles otsima hosti IP-aadressi enne, kui see saab ping-sõnumi saata.

Hostinimi tõlgitakse IP-aadressid DNS (*Domain Name System*) kaudu. DNS on ülemaailmne võrguinfo kataloog, mis säilitab erinevaid andmeid, kuid kõige tuntum on *A record* (A tähendab aadressi). A kaardistab nime (nt www.google.com) IPv4 aadressiks. Klientprogrammid nagu veebibrauserid otsivad neid andmestikke, et leida veebisaidi või võrgu aadress. Veebisaidi looja peab üles seadma DNS-i kirje, et kasutajad pääseksid saidile nime kaudu. See tähendab, et domeeninimi tuleb registreerida. Kui DNS läheb maha, siis enamus kasutajaid ei saa saidile enam ligi. DNS-domeene kasutatakse ka HTTP turvalisuse jaoks, sh SSL-i krüpteerimine ja küpsiseprivaatsus. DNS-i kliendikood (*resolver*) on ehitatud iga operatsioonisüsteemi sisse. Programmid nagu NC ja ping ning ka veebibrauserid saavad seda kasutada.

Hosti käsk (*host command*) ```host eesti.ee``` on baastööriist, et otsida DNS-st kirjeid. See käsk esitab päringu operatsioonisüsteemi võrgule, mis tavaliselt lõppeb sellega, et saadetakse päring misiganes DNS-i serverile, mis on seadistatud saatja arvutil kasutama.

"Host"-le sarnane tööriist on ```dig```, mis pakub samalaadset infot, aga rohkem tehnilisemat laadi. ```host``` näitab rohkem inimesele loetavamat infot inglise keeles. ```dig``` näitab sama infot, mis on rohkem loetavat skriptidele ja on sarnasem sellele, kudias seda infot talletatakse DNS-i serveri *configuration file*-s.

DNS-i kirjeid on väga palju erinevaid. CNAME kirjet kasutatakse aliaste tegemiseks ühest nimest teise. See tähendab, et üks nimi on alias ja teine on ametlik nimi. AAAA kirje on samaväärne IPv6-ga ehk see on lihtsalt IPv6 aadress. NS-kirje on DNS-i nimiserver. Kindla domeeni NS-kirje näitab, mis DNS serveritel on selle domeeni info. DNS on jaotatud kataloog. Üle maailma on mitmeid põhiservereid (*root server*), mis suunavad päringud ülemise leveli domeeniserveritesse, nt .com, mis omakorda suunab päringud kindlate domeenide serveritesse. See tähendab, et ükski DNS-server ei pea teadma kõikide nimede ja domeenide kirjeid. Kindla domeeni kirjed, nt A-kirje, on leitab selle domeeni autoriteetsetest nimiserveritest. *Resolver* suhtleb lähedalasuva vahemälu-DNS-serveriga (*caching DNS server*). Kodukasutaja jaoks võib see olla kohalik koduruuter või hästitunud DNS-server nagu Google Public DNS. Kui vastuvõttev server saab kliendilt päringu, siis esmalt konsulteerib kohaliku vahemäluga (*local cache*). Kui vahemälu ei tea, mis kirjega on tegu, siis vahemälu-server lahendab päringu tagasipöörduvalt ehk edastab selle sobivatesse nimiserveritesse - esmalt *root server*-sse, seejärel ülemise leveli domeenile ja siis madalama leveli domeenile kuni lõpuks saab vastuse tagasi. Seejärel see hoiustab seda tulemust oma vahemälus, et ei peaks sama päringut uuesti jooksutama iga kord, kui otsida nt google.com vms. Lõpuks saab lõpptulemuse tagasta kliendile. Samas võib vahemälus hoiustamine põhjustada probleeme. Kui tahta oma saiti liigutada ühelt IP-aadressilt teisele ja mõnel DNS-il on vahemälus vana aadress, siis klient, kes kasutab vahemälu, näeb vana aadressi. Sellise probleemi vältimiseks on DNS-i kirjetel TTL (*time to live*), mis ütleb, kui kaua saab seda vahemälus hoiustada (nt 60 s).

Domeeninimed on vajalikud HTTP mitmetele lisadele, näiteks küpsise turvalisus ja SSL. Üksik veebiserver saab hallata päringuid mitmelt saidilt, mis teeb vahet domeeninimedel. Selle tõttu on HTTP-päringutel hostipäis (*host header*). Veebiserverile peab ütlema, mis hosti nimega(domeeninimega) soovid suhelda. Hostipäis on vajalik HTTP-päringute jaoks. Kui veebiäpp salvestab küpsise, siis see küpsis seadistatakse kindlale domeeninimele. SSL-krüpteeringu sertifikaadid seatakse kindlatele domeenidele. Liikluse krüpteerimine brauseri ja serveri vahel aitab ära hoida vahelolevatel võrkudel lugeda privaatset infot. Samuti aitab see kasutaja brauselt ära tunda, et sait, millelt infot saame, on tegelikult ka see sait, mida kasutame.

Kaks peamist interneti protokolli kasutusel on v4 ja v6. v4 on vanem, mida kasutab suurem osa internetiliiklusest tänapäeval.IPv4 aadress koosneb 4 arvust. iga arv on 1 bait ehk 8 bitti - nende väärtus võib olla 0-255.


## Addressing and Networks

Kõiki võimalikke 32-bitiseid väärtuseid ei kasutata päris aadressidena. Mõned neist on reserveeritud spetsiaalsete protokollide jaoks, sisemisteks privaatvõrkude jaoks, testimiseks või dokumenteerimiseks. Üle kaheksandiku kõikist IPv4 aadressidest on jäetud kõrvale millekski muuks kui avalikeks aadressideks. Aga isegi siis, kui kasutada kõiki 32-bitilisi aadresse, siis neist ei jätkuks.

Kõik arvutid, mis on samas võrguplokis, on sarnase IP-aadressiga ning saavad omavahel suhelda otse, mite läbi ruuteri. Aga kõik võrgud ei ole sama suurusega. Ehk oluline ei ole ainult võrgu algusnumbrid vaid ka pikkus. Mida pikem on võrgu eesliide, seda väheb 32-bitist aadressi on alles eristamaks kindlaid hoste. Mida lühem eesliide, seda rohkem aadresse on hosti jaoks ja seda suurem võrk on. Võrgu eesliite pikkus tuleb valida võrku üles seades. Kui on 24-bitise eesliitega võrk, siis 8 biti jääb üle aadressi hosti osa jaoks. Seda märgitakse /24. Kui võrgu eesliide on lühem, nt 22 biti, siis jääb üle 10 biti ja seda märgitakse /22. Eesliite pikkust ei näe lihtsalt aadressi vaadates.

Teine võimalus märkida võrgu suurust on alamvõrgumask (*subnet mask*), mida sageli kasutatakse võrgu seadistamisel. Nagu IPv4 aadressid on ka alamvõrgumaskid 32-bitiste väärtustega.

Aadressid kuuluvad liidestele, mitte hostidele. Host võib omada mitut võrguliidest ja iga liides võib omaga 0 või mitu aadressi. Arvutil saab olla näiteks etherneti, wifi ja *loopback* liides. *Loopback* liidesel on peaaegu alati IP-aadressiks 127.0.0.1 ja nimeks *localhost* - see laseb programmidel kasutada protokollivirnasid, et suhelda sama hosti teiste programmidega. Linuxis saab liidesed leida käsuga ```ip addr show```.

Koduruuteril on üks liides, mis läheb internetiteenuse pakkuja juurde, ning teine või mitu liidest, mis on kohalikud, näiteks etherneti või wifi omad. Kohalikus võrgus (LAN) on minu arvuti ja seadmed. Laivõrk (WAN) on liides, mis ühendub ülejäänud internetiga. Ruuter on seade, mis ühendab kaht erinevat IP-võrku. See käitub väravana. Kui ühe võrgu hostid tahavad saata liiklust teise võrgu hostile, siis see liiklus toimub läbi ruuteri. Kui enamus hostidel on üks liides IPv4 aadressiga, siis ruuteril on kaks või rohkem. Kohaliku võrgu host teab vaikimisi ruuterit, milleks on ruuter, mis on ülejäänud internetiga ühendatud.


## Protocol Layers

Võrguprotokollid koosnevad mitmest kihist. Nendel kihtidel on omad ülesanded ning ülemiste kihtide töö sõltub sellest, kas alumised kihid on oma ülesanded edukalt ära teinud. Käsuga ```tcpdump``` saab näha igat võrguliiklust hosti ja võrgu vahel (mitte ainult TCP). Sama käsuga saab näha ka neid pakke, mida arvuti kasutab veebiserveriga suhtlemiseks.

```printf 'HEAD / HTTP/1.1\r\nHost: example.net\r\n\r\n' | nc example.net 80``` näitab pakke, mis liiguvad arvuti ja veebilehe vahel. Iga kirje lõpus on length, mis näitab, kui palju andmeid saadeti pakkides. Enamus pakkides andmeid ei ole (length on 0). See näitab, et enne, kui klient ja server saavad andmeid vahetada, peavad nad ühenduse looma. Pärast andmete vahetamist peavad nad ühenduse katkestama.

Kui klient saadab serverile sõnumi või vastupidi, siis sõnumi kohale jõudmiseks kulub alati mingi aeg ehk sõnum ei jõua samal hetkel kohale kui välja saadeti. Kui klient saadab kaks sõnumit, siis server hakkab esimesele sõnumile vastama kohe pärast selle nägemist ning server ei saa esimest vastust muuta teise päringu järgi, kuna ei ole seda näinud veel. Kui klient on esimese vastuse saanud, siis saab saata serverisse järgmise päringu. Selline lähenemine kehtib ainult HTTP kihi kohta - see ei näita individuaalsete IP-pakkide saatmist.

Kui TCP ühendus avaneb, peavad klient ja server jõudma kokkuleppele, et see ühendus eksisteerib. Enne, kui midagi juhtub, teab klient serveri IP-aadressi ja pordinumbrit, aga server ei tea kliendist midagi. Seega peab klient saatma serverile sõnumi, mis sisaldab muuhulgas kliendi IP-aadressi ja pordinumbrit ning sõnumit, et klient soovib serveri ning selle IP ja pordiga ühenduda. TCP teeb sellest isegi enam - see jälgib ka andmeid, mida igasse lõpp-punkti saadetakse, kas andmed sinna kohale jõuavad ning tagab, et rakendus näeb andmeid õiges järjekorras (isegi, kui võrk saadab käsud teises järjestuses). TCP saab jälgida järjestust selliselt, et paneb järjekorranumbri igale pakile juurde. Lõpp-punkt saadab kinnituse, kui on kätte saanud kindla järjekorranumbri ja kui pakk läheb kaduma, siis algsaatja märkab, et pole kinnitust ning saadab paki uuesti. Kõigi selliste pakkide length on 0.

Järjekorranumbrid on vajalikud, et jälgida, kas kõik pakid on kohale toimetatud ja kätte saadud. Võrk saab pakke kohale toimetada "vales" järjekorras. Iga lõpp-punkt operatsioonisüsteemis (mitte brauseris, veebiserveris jne) hoiab mälus nii palju ruumi, et see mahutaks saabuvaid andmeid. Mälu kasutab järjekorranumbreid positsioonina.

Igal tcpdump paki kirjel on osa *Flags*, millele järgneb kandilistes sulgudes tähed või punktid. *Flag* on tõeväärtus (õige või vale), mis hoiustatakse mälus ühe bitina. Kui *Flag* on 1, siis see on *set*, kui 0, siis *cleared* või *unset*. TCP-l on 6 põhilist *flag*-i: S, F, P, R, ., U.

Esimesel pakil, mis saadetakse TCP sessiooni algatama, on alati SYN lipuke (```Flags [S]```). Sellel pakil on ka uus, suvaline järjekorranumber. Kui server nõustub ühendusega, siis see saadab paki tagasi, millel on SYN ja ACK lipukesed (```Flags [S.]```). Lõpuks tunnistab klient SYN ja ACK paki saamist saates ise ACK paki serverile.

Kui iga lõpp-punkt on lõpetanud andmete saatmise ühendusse, siis see saab saata FIN paki, et viidata lõpetamisele. Teine lõpp-punkt saadab ACK, et viidata FIN kättesaamisele.

TCP ei saada kogu infokogumit korraga ruuterisse, sest sellisel juhul ei mahuks sinna tol hetkel midagi muud ehk keegi teine ei saaks selles võrgus midagi saata. TCP hakkab andmeid saata aeglaselt ja tõstab kiirust vaid siis, kui teine pool saab sammu pidada kinnituste tagasi saatmisega. Kui pakid on saadetud, siis TCP aeglustab ja seejärel suurendab taas kiirust. Kui ruuter laseks andmetel kuhjuda, siis ühendus lõpuks lõppeks. 

Kui serverilt vastuse saamine võtab liiga kaua aega, siis TCP loobub ühendamisest ja annab errori rakendusele. TCP-l on mitu sisse ehitatud taimerit sellise olukorra jaoks.


## Big Networks

Internetti "hoiavad koos" ruuterid, mis edastavad pakke allikast sihtkohta. Iga edastus, mis läheb ühest masinast teise, on *hop*. *traceroute* tööriista kasutades saab näha enda võrgust teistesse serveritesse minevaid *hop*'e: ```traceroute google.com```.

Arvuti ei oska öelda, kui kaua võtab pakil aega, et jõuda õhest kohast teise otsa. Aga arvui oskab öelda, kui palju aega on möödunud hetkest, kui pakk teele saadeti ja vastus saadi.

Igal pakil on "aeg elada" (*TTL*), mis väheneb ühe võrra iga kord, kui pakk jõuab mõnda rutterisse ja/või sihtkohta. Kui ruuterid on valesti seadistatud ja pakid liiguvad lõputus ringis, siis TTL jõuab lüpuks nulli ja aegub - see aitab vältida suurtel networkidel kokku jooksmast liiga suure liikluse tõttu. Kui pakk aegub, siis ruuter, mis selle viimasena kätte sai, saadab errori sõnumi paki originaalsaatjale, kus on kirjas rutueri aadress, kus pakk aegus.

Latentsusaeg (*latency*) on paki kulgemise aeg lähtekohast lõplikku sihtkohta. Latentsusaega mõjutab kaugus ja *hop*'de arv. Interneti signaalid iiguvad valguskiirusega, aga ruuteritel võtab aega, et neid signaale ümber lülitada.

Ülekandemaht (*bandwidth*) on edastuskanali läbilaskevõime ehk suurim võimalik või vajalik andmeedastuskiirus kanalis, mõõtühik bitt sekundis (bit/s) või bait sekundis (B/s).

Tulemüür on vahend, mis filtreerib sissetulevat ja väljaminevat liiklust võrgus. Tuemüür kuulub suuremasse mõistesse nagu *middlebox*, mis on vahend võrgu inspekteerimiseks, muutmiseks ja filtreerimiseks.
