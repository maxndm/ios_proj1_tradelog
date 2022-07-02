# IOS - Operační systémy

<html>

<h1 class="title">1. Úloha IOS (2021)</h1>
<p>Hodnocení: 15/15 bodů
<div class="row">
<div class="col-xl-10"><h1 id="popis-úlohy">Popis úlohy</h1>
<p>Cílem úlohy je vytvořit skript pro analýzu záznamu systému pro obchodování na burze. Skript bude filtrovat záznamy a poskytovat statistiky podle zadání úživatele.</p>
<h1 id="zjednodušený-úvod-do-problematiky">Zjednodušený úvod do problematiky</h1>
<p>Na burze se obchoduje s cennými papíry (např. akcie společností, dluhopisy), komoditami (např. ropa, zelí) apod. Každý obchodovaný artikl má jednoznačný identifikátor, tzv. <em>ticker</em> (např. akcie firmy Intel mají na burze NASDAQ ticker <code>INTC</code>, bitcoin může mít přiřazený ticker <code>BTC</code>). Cena artiklů se mění v čase. Obchodník na burze vstupuje do <em>pozic</em>, buď tak, že koupí artikl a očekává, že jeho cena poroste, aby jej pak prodal za vyšší cenu (tzv. <em>dlouhá</em> pozice), nebo že artikl prodá a očekává, že jeho cena klesne, aby jej poté mohl koupit levněji (tzv. <em>krátká</em> pozice). Obchodník může prodat i artikl, který právě nevlastní (v reálu to funguje tak, že si ho od někoho, kdo ho vlastní, “vypůjčí”, prodá jej, a potom ho koupí za nižší cenu a “vrátí”). V našem případě budeme uvažovat, že obchodníkův systém posílá na burzu příkazy k <em>nákupu</em> (<code>buy</code>) nebo <em>prodeji</em> (<code>sell</code>) určitého množství jednotek artiklu označeného nějakým tickerem.</p>
<h1 id="specifikace-rozhraní-skriptu">Specifikace rozhraní skriptu</h1>
<p><strong>JMÉNO</strong></p>
<ul>
<li><code>tradelog</code> - analyzátor logů z obchodování na burze</li>
</ul>
<p><strong>POUŽITÍ</strong></p>
<ul>
<li><code>tradelog [-h|--help] [FILTR] [PŘÍKAZ] [LOG [LOG2 [...]]</code></li>
</ul>
<p><strong>VOLBY</strong></p>
<ul>
<li><code>PŘÍKAZ</code> může být jeden z:
<ul>
<li><code>list-tick</code> – výpis seznamu vyskytujících se burzovních symbolů, tzv. “tickerů”.</li>
<li><code>profit</code> – výpis celkového zisku z uzavřených pozic.</li>
<li><code>pos</code> – výpis hodnot aktuálně držených pozic seřazených sestupně dle hodnoty.</li>
<li><code>last-price</code> – výpis poslední známé ceny pro každý ticker.</li>
<li><code>hist-ord</code> – výpis histogramu počtu transakcí dle tickeru.</li>
<li><code>graph-pos</code> – výpis grafu hodnot držených pozic dle tickeru.</li>
</ul></li>
<li><code>FILTR</code> může být kombinace následujících:
<ul>
<li><code>-a DATETIME</code> – after: jsou uvažovány pouze záznamy PO tomto datu (bez tohoto data). <code>DATETIME</code> je formátu <code>YYYY-MM-DD HH:MM:SS</code>.</li>
<li><code>-b DATETIME</code> – before: jsou uvažovány pouze záznamy PŘED tímto datem (bez tohoto data).</li>
<li><code>-t TICKER</code> – jsou uvažovány pouze záznamy odpovídající danému tickeru. Při více výskytech přepínače se bere množina všech uvedených tickerů.</li>
<li><code>-w WIDTH</code> – u výpisu grafů nastavuje jejich šířku, tedy délku nejdelšího řádku na <code>WIDTH</code>. Tedy, <code>WIDTH</code> musí být kladné celé číslo. Více výskytů přepínače je chybné spuštění.</li>
</ul></li>
<li><code>-h</code> a <code>--help</code> vypíšou nápovědu s krátkým popisem každého příkazu a přepínače.</li>
</ul>
<h1 id="popis">Popis</h1>
<ol type="1">
<li>Skript filtruje záznamy z nástroje pro obchodování na burze. Pokud je skriptu zadán také příkaz, nad filtrovanými záznamy daný příkaz provede.</li>
<li>Pokud skript nedostane ani filtr ani příkaz, opisuje záznamy na standardní výstup.</li>
<li>Skript umí zpracovat i záznamy komprimované pomocí nástroje gzip (v případě, že název souboru končí <code>.gz</code>).</li>
<li>V případě, že skript na příkazové řádce nedostane soubory se záznamy (<code>LOG</code>, <code>LOG2</code> …), očekává záznamy na standardním vstupu.</li>
<li>Pokud má skript vypsat seznam, každá položka je vypsána na jeden řádek a pouze jednou. Není-li uvedeno jinak, je pořadí řádků dáno abecedně dle tickerů. Položky se nesmí opakovat.</li>
<li>Grafy jsou vykresleny pomocí ASCII a jsou otočené doprava. Každý řádek histogramu udává ticker. Kladná hodnota či četnost jsou vyobrazeny posloupností znaku mřížky <code>#</code>, záporná hodnota (u <code>graph-pos</code>) je vyobrazena posloupností znaku vykřičníku <code>!</code>.</li>
</ol>
<h1 id="podrobné-požadavky">Podrobné požadavky</h1>
<ol type="1">
<li><p>Skript analyzuje záznamy (logy) pouze ze zadaných souborů v daném pořadí.</p></li>
<li><p>Formát logu je CSV kde oddělovačem je znak středníku <code>;</code>. Formát je řádkový, každý řádek odpovídá záznamu o jedné transakci ve tvaru</p>
<pre><code>DATUM A CAS;TICKER;TYP TRANSAKCE;JEDNOTKOVA CENA;MENA;OBJEM;ID</code></pre>
<p>kde</p>
<ul>
<li><code>DATUM A CAS</code> jsou ve formátu <code>YYYY-MM-DD HH:MM:SS</code></li>
<li><code>TICKER</code> je řetězec neobsahující bílé znaky a znak středníku</li>
<li><code>TYP TRANSAKCE</code> nabývá hodnoty <code>buy</code> nebo <code>sell</code> značící nákup resp. prodej</li>
<li><code>JEDNOTKOVA CENA</code> je cena za jednu akcii, jednotku komodity, atp. s přesností na maximálně dvě desetinná místa; jako oddělovač jednotek a desetin slouží znak tečky <code>.</code>; Např. <code>1234567.89</code></li>
<li><code>MENA</code> je třípísmenný kód měny, např <code>USD</code>, <code>EUR</code>, <code>CZK</code>, <code>SEK</code>, <code>GBP</code> atd.</li>
<li><code>OBJEM</code> značí množství jednotek (akcií, jednotek komodity atp.) v transakci</li>
<li><code>ID</code> je identifikátor transakce (řetězec neobsahující bílé znaky a znak středníku)</li>
</ul>
<p><em>Hodnota transakce</em> je <code>JEDNOTKOVA CENA</code> * <code>OBJEM</code>. Příklad záznamů:</p>
<pre><code>2021-07-29 23:43:13;TSM;buy;667.90;USD;306;65fb53f6-7943-11eb-80cb-8c85906a186d
2021-07-29 23:43:15;BTC;sell;50100;USD;5;65467d26-7943-11eb-80cb-8c85906a186d</code></pre>
<ul>
<li>První záznam značí nákup 306 akcií firmy TSMC (ticker <code>TSM</code>) za cenu 667.90 USD / akcie. Hodnota transakce je tedy 204377.40 USD.</li>
<li>Druhý záznam značí prodej 5 bitcoinů (ticker <code>BTC</code>) za cenu 50 100 USD / bitcoin. Hodnota transakce je tedy 250500.00 USD.</li>
</ul></li>
<li><p>Předpokládejte, že měna je u všech záznamů stejná (není potřeba ověřovat).</p></li>
<li><p>Skript žádný soubor nemodifikuje. Skript nepoužívá dočasné soubory.</p></li>
<li><p>Můžete předpokládat, že záznamy jsou ve vstupních souborech uvedeny chronologicky a je-li na vstupu více souborů, jejich pořadí je také chronologické.</p></li>
<li><p><em>Celkový zisk z uzavřených pozic</em> (příkaz <code>profit</code>) se spočítá jako <em>suma hodnot <code>sell</code> transakcí</em> - <em>suma hodnot <code>buy</code> transakcí</em>.</p></li>
<li><p><em>Hodnota aktuálně držených pozic</em> (příkazy <code>pos</code> a <code>graph-pos</code>) se pro každý ticker spočítá jako <em>počet držených jednotek</em> * <em>jednotková cena z poslední transakce,</em> kde <em>počet držených jednotek</em> je dán jako <em>suma objemů <code>buy</code> transakcí</em> - <em>suma objemů <code>sell</code> transakcí</em>.</p></li>
<li><p>Pokud není při použití příkazu <code>hist-ord</code> uvedena šířka <code>WIDTH</code>, pak každá pozice v histogramu odpovídá jedné transakci.</p></li>
<li><p>Pokud není při použití příkazu <code>graph-pos</code> uvedena šířka <code>WIDTH</code>, pak každá pozice v histogramu odpovídá hodnotě 1000 (zaokrouhleno na tisíce směrem k nule, tj. hodnota 2000 bude reprezentována jako <code>##</code> zatímco hodnota 1999.99 jako <code>#</code> a hodnota -1999.99 jako <code>!</code>.</p></li>
<li><p>U příkazů <code>hist-ord</code> a <code>graph-pos</code> s uvedenou šířkou <code>WIDTH</code> při dělení zaokrouhlujte směrem k nule (tedy např. při <code>graph-pos -w 6</code> a nejdelším řádku s hodnotou 1234 bude řádek s hodnotou 1234 vypadat takto <code>######</code>, řádek s hodnotou 1233.99 takto <code>#####</code> a řádek s hodnotou -1233.99 takto <code>!!!!!</code>).</p></li>
<li><p>Pořadí argumentů stačí uvažovat takové, že nejřív budou všechny přepínače, pak (volitelně) příkaz a nakonec seznam vstupních souborů (lze tedy použít <code>getopts</code>). Podpora argumentů v libovolném pořadí je nepovinné rozšíření, jehož implementace může kompenzovat případnou ztrátu bodů v jiné časti projektu.</p></li>
<li><p>Předpokládejte, že vstupní soubory nemůžou mít jména odpovídající některému příkazu nebo přepínači.</p></li>
<li><p>V případě uvedení přepínače <code>-h</code> nebo <code>--help</code> se vždy pouze vypíše nápověda a skript skončí (tedy, pokud by za přepínačem následoval nějaký příkaz nebo soubor, neprovede se).</p></li>
<li><p>Při výpisu pomocí příkazů <code>pos</code>, <code>last-price</code>, <code>hist-ord</code> a <code>graph-pos</code> musí být tickery zarovnány doleva a dvojtečka na 11. pozici na řádku (výplň proveďte pomocí mezer). U příkazů <code>hist-ord</code> a <code>graph-pos</code> je za dvojtečkou na všech řádcích právě jedna mezera (případně žádná, pokud v pravém sloupci daného řádku nic není), u příkazů <code>pos</code> a <code>last-price</code> jsou hodnoty v pravé části výpisu formátovány tak, aby (v případě neprázdného výpisu) byla na řádku s nejdelší řetězcovou reprezentací hodnoty (tj. včetně znaménka) mezi dvojtečkou a hodnotou právě jedna mezera a ostatní řádky byly zarovnány doprava vzhledem k délce tohoto řádku (vizte příklady výpisů níže).</p></li>
</ol>
<h1 id="návratová-hodnota">Návratová hodnota</h1>
<ul>
<li>Skript vrací úspěch v případě úspěšné operace. Interní chyba skriptu nebo chybné argumenty budou doprovázeny chybovým hlášením a neúspěšným návratovým kódem.</li>
</ul>
<h1 id="implementační-detaily">Implementační detaily</h1>
<ol type="1">
<li><p>Skript by měl mít v celém běhu nastaveno <code>POSIXLY_CORRECT=yes</code>.</p></li>
<li><p>Skript by měl běžet na všech běžných shellech (<code>dash</code>, <code>ksh</code>, <code>bash</code>). Pokud použijete vlastnost specifickou pro nějaký shell, uveďte to pomocí direktivy interpretu na prvním řádku souboru, např. <code>#!/bin/bash</code> nebo <code>#!/usr/bin/env bash</code> pro <code>bash</code>. Můžete použít GNU rozšíření pro <code>sed</code> či <code>awk</code>. Jazyky Perl, Python, Ruby, atd. povoleny nejsou.</p>
<p><strong>UPOZORNĚNÍ:</strong> některé servery, např. <code>merlin.fit.vutbr.cz</code>, mají symlink <code>/bin/sh -&gt; bash</code>. Ověřte si proto, že skript skutečně testujete daným shellem. Doporučuji ověřit správnou funkčnost pomocí virtuálního stroje níže.</p></li>
<li><p>Skript musí běžet na běžně dostupných OS GNU/Linux, BSD a MacOS. Studentům je k dispozici virtuální stroj s obrazem ke stažení zde: <a href="http://www.fit.vutbr.cz/~lengal/public/trusty.ova.cs">http://www.fit.vutbr.cz/~lengal/public/trusty.ova</a> (pro VirtualBox, login: <code>trusty</code> / heslo: <code>trusty</code>), na kterém lze ověřit správnou funkčnost projektu.</p></li>
<li><p>Skript nesmí používat dočasné soubory. Povoleny jsou však dočasné soubory nepřímo tvořené jinými příkazy (např. příkazem <code>sed -i</code>).</p></li>
<li><p>Čísla vypisujte v desítkovém zápisu s přesností na dvě desetinná místa. Pozor, některé nástroje (např. <code>awk</code>) mohou větší čísla vypisovat implicitně pomocí vědeckého zápisu.</p></li>
</ol>
<h1 id="odevzdání-projektu">Odevzdání projektu</h1>
<p>Odevzdávejte pouze skript <code>tradelog</code> (nebalte ho do žádného archivu). Odevzdejte do IS, termín Projekt 1.</p>
<h1 id="rady">Rady</h1>
<ul>
<li>Dobrá dekompozice problému na podproblémy Vám může značně ulehčit práci a předejít chybám.</li>
<li>Naučte se <em>dobře</em> používat funkce v shellu</li>
</ul>
<h1 id="příklady-použití">Příklady použití</h1>
<ul>
<li>Ukázky záznamů nástroje pro obchodování na burze jsou dostupné zde: <a href="https://pajda.fit.vutbr.cz/ios/ios-21-1-logs">https://pajda.fit.vutbr.cz/ios/ios-21-1-logs</a></li>
</ul>
<p>Příklady:</p>
<pre><code>$ cat stock-2.log | head -n 5 | ./tradelog
2021-07-29 15:30:42;MSFT;sell;240.07;USD;327;65fad854-7943-11eb-929d-8c85906a186d
2021-07-29 15:31:12;MA;sell;314.91;USD;712;65fae24a-7943-11eb-9171-8c85906a186d
2021-07-29 15:31:32;BAC;buy;34.16;USD;635;65fae466-7943-11eb-8f48-8c85906a186d
2021-07-29 15:37:09;BAC;sell;36.67;USD;897;65fae614-7943-11eb-9ccb-8c85906a186d
2021-07-29 15:43:02;JPM;sell;146.77;USD;190;65fae79a-7943-11eb-8977-8c85906a186d</code></pre>
<pre><code>$ ./tradelog -t TSLA -t V stock-2.log
2021-07-29 17:06:57;TSLA;buy;757.57;USD;812;65fafb04-7943-11eb-8d41-8c85906a186d
2021-07-29 17:58:18;V;sell;215.31;USD;406;65fb0662-7943-11eb-87fe-8c85906a186d
2021-07-29 18:12:27;TSLA;sell;729.75;USD;482;65fb0892-7943-11eb-867f-8c85906a186d
2021-07-29 18:55:19;V;sell;217.92;USD;210;65fb1238-7943-11eb-86e2-8c85906a186d
2021-07-29 19:19:26;TSLA;sell;700.75;USD;457;65fb1792-7943-11eb-8abf-8c85906a186d
2021-07-29 19:27:39;TSLA;buy;710.79;USD;633;65fb19b8-7943-11eb-a5d9-8c85906a186d
2021-07-29 20:06:53;V;sell;218.72;USD;272;65fb237c-7943-11eb-83a3-8c85906a186d
2021-07-29 20:59:16;V;sell;196.54;USD;92;65fb2c32-7943-11eb-9dd3-8c85906a186d
2021-07-29 21:03:15;V;buy;188.60;USD;605;65fb2d4a-7943-11eb-8804-8c85906a186d
2021-07-29 21:17:37;V;sell;222.52;USD;447;65fb2f7a-7943-11eb-8f28-8c85906a186d
2021-07-29 21:18:18;TSLA;buy;733.96;USD;720;65fb3092-7943-11eb-992a-8c85906a186d
2021-07-29 21:50:25;V;sell;212.58;USD;2833;65fb3a2e-7943-11eb-8e0b-8c85906a186d
2021-07-29 22:10:55;TSLA;sell;718.31;USD;3794;65fb3f88-7943-11eb-a371-8c85906a186d
2021-07-29 22:21:31;TSLA;sell;681.74;USD;7122;65fb41a4-7943-11eb-a09f-8c85906a186d
2021-07-29 23:01:47;TSLA;sell;707.03;USD;1578;65fb4a50-7943-11eb-9f6e-8c85906a186d
2021-07-29 23:21:11;TSLA;buy;679.27;USD;9655;65fb4fb4-7943-11eb-8199-8c85906a186d
2021-07-29 23:43:13;TSLA;buy;667.90;USD;306;65fb53f6-7943-11eb-80cb-8c85906a186d
2021-07-29 23:48:29;V;buy;195.52;USD;2003;65fb5824-7943-11eb-9b59-8c85906a186d</code></pre>
<pre><code>$ ./tradelog -t CVX stock-4.log.gz | head -n 3
2021-09-27 05:12:30;CVX;sell;108.17;USD;88;8f229a62-7945-11eb-a6fb-8c85906a186d
2021-09-27 13:57:48;CVX;sell;94.81;USD;5374;8f22ec38-7945-11eb-8c68-8c85906a186d
2021-09-27 14:52:50;CVX;sell;89.22;USD;7759;8f22f46c-7945-11eb-9bb2-8c85906a186d</code></pre>
<pre><code>$ ./tradelog list-tick stock-2.log
AAPL
AMZN
BABA
BAC
DIS
FB
GOOG
GOOGL
JNJ
JPM
MA
MSFT
NVDA
PG
PYPL
TSLA
TSM
UNH
V
WMT</code></pre>
<pre><code>$ ./tradelog profit stock-2.log
-58863165.03</code></pre>
<pre><code>$ ./tradelog -t TSM -t PYPL profit stock-2.log
-577302.62</code></pre>
<pre><code>$ ./tradelog pos stock-2.log
AMZN      : 64645275.64
GOOGL     :  7914389.08
NVDA      :  2540507.69
DIS       :  1925621.88
TSM       :  1266217.38
JPM       :   937220.31
BABA      :   444692.64
BAC       :   323899.29
JNJ       :    81769.32
FB        :    42673.05
WMT       :     2423.34
MSFT      :  -321051.64
V         :  -322999.04
PYPL      :  -502892.46
MA        :  -569746.42
TSLA      :  -872945.30
PG        : -1138885.10
AAPL      : -1190996.48
UNH       : -1781240.88
GOOG      : -9846258.51</code></pre>
<pre><code>$ ./tradelog -t TSM -t PYPL -t AAPL pos stock-2.log
TSM       :  1266217.38
PYPL      :  -502892.46
AAPL      : -1190996.48</code></pre>
<pre><code>$ ./tradelog last-price stock-2.log
AAPL      :  133.88
AMZN      : 3496.04
BABA      :  245.28
BAC       :   38.61
DIS       :  207.48
FB        :  275.31
GOOG      : 1975.97
GOOGL     : 1990.04
JNJ       :  155.16
JPM       :  135.77
MA        :  333.38
MSFT      :  237.64
NVDA      :  629.93
PG        :  124.70
PYPL      :  279.54
TSLA      :  667.90
TSM       :  140.41
UNH       :  321.06
V         :  195.52
WMT       :  134.63</code></pre>
<pre><code>$ ./tradelog hist-ord stock-2.log
AAPL      : ##
AMZN      : #####
BABA      : ####
BAC       : #####
DIS       : #####
FB        : ####
GOOG      : ######
GOOGL     : ########
JNJ       : ##
JPM       : ######
MA        : ####
MSFT      : ####
NVDA      : ######
PG        : #####
PYPL      : ####
TSLA      : ##########
TSM       : ##
UNH       : ######
V         : ########
WMT       : ####</code></pre>
<pre><code>$ ./tradelog -w 100 graph-pos stock-6.log
AAPL      : !!!!!!!!!!!!
AMZN      : !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
BABA      : ####
BAC       : ###
DIS       : ###################
FB        :
GOOG      : !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
GOOGL     : ################################################################################
JNJ       :
JPM       : #########
MA        : !!!!!
MSFT      : !!!
NVDA      : #########################
PG        : !!!!!!!!!!!
PYPL      : !!!!!
TSLA      : !!!!!!!!
TSM       : ############
UNH       : !!!!!!!!!!!!!!!!!!
V         : !!!
WMT       :</code></pre>
<pre><code>$ ./tradelog -w 10 -t FB -t JNJ -t WMT graph-pos stock-6.log
FB        : #####
JNJ       : ##########
WMT       :</code></pre>
<pre><code>$ cat /dev/null | ./tradelog profit
0.00</code></pre></div>
   </body>
</html>