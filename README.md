# P2-Troner-2018

1 Descripció general

A l'any 1982 es va llançar al mercat un joc arcade anomenat TRON, inspirat en la pel·lícula que
Walt Disney va presentar el mateix any.
TRON és un joc multijugador en el qual cada un dels jugadors controla un punter lluminós per
un escenari. Cada un d'aquests punters representa una moto que a mesura que avança deixa
una estela de llum la qual és representada amb una línia d'un color. L'objectiu del joc és que
cada un dels jugadors es mogui per la pantalla fins que perdi un dels dos. El motiu pel qual un
dels jugadors pot perdre la partida és que el seu punter col·lideixi amb una paret (límit de
l’escenari) o amb una de les esteles de llum, ja sigui la pròpia o bé la de l’altre jugador. La
captura següent és un exemple d’una partida entre dos jugadors, en aquest cas el jugador de
rossa acaba de perdre la partida, ja que ha col·lidit amb l’estela del jugador verd.
Figura 1: Exemple del joc TRON. Captura obtinguda de: http://www.classicgamesarcade.com/game/21670/trongame.html
En aquest context, el projecte Troner tracta d’implementar el joc TRON. Concretament, i
mitjançant una arquitectura client-servidor, els usuaris es connectaran al servidor per tal de
mantenir diferents tipus de partides.


2 Especificació de requeriments

Aquest apartat recull el funcionament i el comportament que s'espera de l'aplicació Troner per
tal de satisfer les especificacions requerides. Tot seguit es pot veure la separació entre les
aplicacions client i servidor, i en cada secció el recull de les funcionalitats que ha de presentar
cada programa.

2.1 Servidor

Com es veurà a continuació, el programa servidor haurà d’emmagatzemar dades dels usuaris
(nickname, paraula de pas, data de registre i data del darrer accés) i informació referent a
l’històric de partides (data de la partida, contrincant i punts aconseguits) en una base de dades
MySQL. Referent a això, és important notar el fet de que el programa servidor és l’únic
programa que té accés a la base de dades, i que la lògica de funcionament del sistema
comprèn l’intercanvi d’informació entre els programes servidor i client mitjançant la
comun

2.1.1 Registrar usuaris

La primera funcionalitat, Registrar usuaris, ha de permetre donar d’alta nous usuaris al
sistema. Tot usuari ha de ser caracteritzat per: un nickname i una paraula de pas. El registre es
pot donar de dues maneres diferents:

  1. De forma local: ha de ser accessible mitjançant una opció del menú del programa
servidor. Aquesta funcionalitat està pensada per ser executada pel personal
administrador. Cal demanar les dades necessàries pel registre i procedir a la seva alta.
  2. De forma remota: aquesta via de registre ha d’estar activa en tot moment mentre el
servidor és en execució. Serà l’encarregada de rebre, mitjançant sockets, informació
enviada pels clients de la seva pròpia opció de registre.
Amb independència de si el registre s’efectua de forma local al servidor (per un usuari
administrador del sistema) o si es tracta d’una petició de registre per part d’algun programa
client, cal tenir en compte un seguit d’aspectes.
El servidor haurà de ser qui comprovi que les dades de registre són vàlides en aquelles
situacions en què cal consultar la base de dades. És a dir, que l’usuari introdueixi un camp buit
en registrar-se pot ser gestionat des del programa client, però que el nom d’usuari ja existeixi
ha de ser verificat pel programa servidor, ja que és aquest qui té accés a la base de dades.
Tanmateix tot allò que comprovi el programa servidor també haurà de ser comprovat pel
programa servidor. A l’igual que pel registre, el servidor és l’encarregat de verificar que
l’usuari/email existeix i que la contrasenya introduïda és l’associada a aquest usuari.

2.1.2 Gestionar usuaris
 
La funcionalitat 2, Gestionar usuaris, s’ha de poder executar des de la interfície gràfica del
servidor. Aquesta ha de mostrar una taula amb les dades de tots els usuaris registrats al
sistema (aquests emmagatzemats a la base de dades). Per cadascun s’ha de visualitzar el
nickname, el punts acumulats, la data que es van registrar i la data del darrer accés al sistema.
Per a cada fila de la taula o usuari registrat s’ha d’oferir un control (per exemple un botó) per
eliminar l’usuari del sistema.

2.1.3 Configuració

Referent a la configuració, el programa servidor ha d’oferir una opció del menú per tal de
configurar el número de port destinat a rebre les connexions dels jugadors i ha d’oferir els
controls necessaris per iniciar i aturar el servei.
Si no s’executa aquesta opció, el servidor no pot atendre als usuaris i el programa client no té
cap utilitat. Un cop l’usuari administrador configuri el número de port i iniciï el sistema,
s’arrencaran les funcions del servidor i a partir d’aquell moment els clients es podran
connectar per tal de registrar-se, autenticar-se, jugar, etcètera.
El servidor també haurà de carregar un conjunt d’informació d’un fitxer de text anomenat
config.json. El qual serà ubicat en el directori arrel del projecte Servidor. Aquest fitxer
contindrà:

   Port de connexió amb la Base de dades
   Direcció IP del servidor de la base de dades (normalment “localhost”)
   Nom de la Base de dades
   Usuari d’accés a la Base de dades
   Contrasenya d’accés a la Base de dades
   Port de comunicació on escoltar les peticions dels programes Client
  Tota aquesta informació s’haurà de carregar al sistema en el moment en que s’iniciï l’aplicació.

2.1.4 Mostrar rànquing

La funcionalitat Mostrar rànquing ha de ser una opció accessible mitjançant el menú de la
interfície gràfica del servidor. Aquesta ha de mostrar, en format taula (component JTable), el
nickname, data del darrer accés i els punts acumulats i actualitzats de tots els usuaris registrats
al sistema. Aquest rànquing ha de ser accessible en qualsevol moment, s’estigui o no duent a
terme una partida.
Els punts que van acumulant els jugadors s’han d’emmagatzemar a una base de dades
relacional MySQL en el servidor.

2.1.5 Mostrar gràfic

D'igual manera que la funcionalitat anterior, l'opció Mostrar gràfic, ha de permetre visualitzar
la gràfica d'un determinat jugador en qualsevol moment, s’estigui o no duent a terme una
partida.
En aquest cas el programa ha de permetre seleccionar el nom del jugador del qual es vol
visualitzar la gràfica, seleccionar el mode de joc del que es vol veure la gràfica i tot seguit
mostrar l’evolució dels punts del jugador en aquest. A l’eix de les X s’han de mostrar les
partides (1, 2, 3, etcètera) i a l’eix de les Y la progressió de puntuació relativa pel mode de joc
pertinent. El nom de l’usuari no ha de ser introduït per teclat, sino que, per tal d’evitar errors,
aquest ha de poder ser seleccionat d’un llistat (per exemple utilitzant el component
JComboBox).
El gràfic haurà de ser generat des del programa, utilitzant les eines de dibuix que ofereix la
llibreria AWT/SWING de Java i escalat de forma conseqüent en funció dels valors mínim i
màxim dels dos eixos de coordenades.

2.2 Client

En aquest apartat es descriuen les opcions que ha d'implementar el programa client, el qual
serà executat en 2 ordinadors per tal de poder mantenir una partida. Aquestes funcionalitats
son:
  1. Configuració
  2. Registrar-se
  3. Iniciar i tancar sessió
  4. Jugar 2x
  5. Jugar 4x
  6. Jugar Campionat
  7. Mostrar rànquing dels modes de joc en directe
Els propers apartats descriuen les característiques i el comportament de cadascuna d’aquestes
funcionalitats del programa client.

2.2.1 Configuració

Aquesta opció del menú haurà de ser executada abans de qualsevol altra. Mitjançant aquesta
funcionalitat l’usuari indicarà la direcció IP del servidor, el número de port al qual connectarse.
Un cop l’usuari accepti aquests paràmetres, el programa ha de comprovar la connexió amb
el servidor i mostrar per pantalla si els paràmetres introduïts són correctes o no.

A partir d’aquest punt, el programa client ha d’habilitar les opcions per tal de registrar-se o bé
autenticar-se.

2.2.2 Registre, iniciar sessió i tancar sessió

Un cop els paràmetres de connexió han estat establerts, seguidament l’usuari podrà: registrarse
o bé autenticar-se com un jugador.
Per tal de registrar-se al sistema, l’usuari haurà d’introduir la següent informació en un
formulari (el for- mat del qual és totalment lliure):
   Nom d’usuari ( ha de ser únic en el sistema)
   Correu (el format del correu ha de ser correcte i ser únic en el sistema)
   Contrasenya (la contrasenya pot contenir caràcters alfanumèrics incloent minúscules i
  majúscules així com tot tipus de caràcters especials. Cal que la contrasenya tingui com
  a mínim una longitud de 6 caràcters així com contingui com a mínim majúscules,
  minúscules i valors numèrics. No obstant, es recomana la lectura de la política de
  contrasenyes del MIT com exemple de quines restriccions s’apliquen en entorns reals.)
   Confirmació de contrasenya (el contingut d’aquest camp ha de coincidir amb el
  contingut del camp "contrasenya")
Quan l’usuari cliqui a enviar el formulari, caldrà comprovar que les dades del formulari
compleixin els requisits (excepte el fet que el nom d’usuari i el correu siguin únics en el
sistema, que es comprovarà quan es faci la petició de registre al servidor) i, en cas que no sigui
així, es mostrarà un missatge explicatiu a la interfície gràfica del client. En cas que en el client
tot sigui correcte s’enviarà la petició de registre al servidor el qual processarà la informació i en
cas que es validi aquesta es procedirà a registrar l’usuari en el sistema. Cal destacar, que en el
servidor, caldrà comprovar de nou que la informació rebuda compleixi els requisits pertinents.
En cas que les dades no siguin validades correctament en el servidor, caldrà notificar-ho al
client i mostrar un missatge d’error explicatiu en la interfície client. En cas de registre
satisfactori, l’usuari podrà autenticar-se i emprar totes les funcionalitats del client a partir
d’aquest moment i s’autenticarà automàticament l’usuari en el sistema.
Per tal d’iniciar sessió́al sistema l’usuari haurà̀d’introduir el seu nom d’usuari o el correu amb
el qual es va registrar en el seu moment i la contrasenya associada. És important remarcar que
s’ha de poder accedir tant amb l’usuari com amb el correu.
Un cop s’enviï̈el formulari al servidor, aquest el validarà̀
i garantirà̀accés si les credencials són
correctes i, en cas contrari, mostrarà̀un error genèric tal que "Les credencials introduïdes són
incorrectes" en qualsevol cas.
Quan l’usuari no vulgui continuar jugant, seleccionarà l’opció de tancar la sessió i es
desconnectarà del servidor.

2.2.3 Pantalla principal i Joc

Un cop l’usuari ha estat autenticat en el sistema, es visualitzarà la pantalla principal del joc. En
aquesta pantalla caldrà que es visualitzi en tot moment el rànquing en viu de el top 10 de
jugadors dels diferents modes de joc. Caldrà també que es pugui tancar sessió així com reconfigurar
els controls de teclat. Per defecte hi haurà una configuració dels controls 
predeterminada. Quan un usuari canviï la configuració de les tecles aquesta persistirà entre
sessions, és a dir, si un usuari canvia la configuració i fa logout i posteriorment torna a entrar,
tindrà quan entri la configuració configurada prèviament. Finalment, caldrà que es permeti
iniciar una partida tal que s’ha de disposar de tres opcions de jugar, una per cada mode de joc.
Els diferents modes de joc disponibles són els següents:
   2X: Partida de dos jugadors simultanis.
   4X: Partida de 4 jugadors simultanis.
   Torneig: Partida de tres etapes on a cada etapa s’elimina al jugador que ha fet menor
  puntuació. Per cada etapa cal aplicar els sistemes de puntuació equivalents al nombre
  de jugadors que hi participen. La ronda inicial de un torneig és de 4 jugadors així que
  caldrà esperar a tenir 4 jugadors per tal de començar un torneig.
En els campionats cada etapa es jugarà al millor de 3 rondes. En els modes 2X i 4X es jugarà
fins que un dels jugadors es desconnecti de la partida. Quan això passi, la resta de jugadors de
la partida tornaran a entrar en cua per aquell mateix mode de joc.
Quan finalitzi una ronda d’una partida s’hauran de repartir les puntuacions als diferents
participants. Per tal de fer aquesta repartició es proposa el següent sistema:
   En 2X el jugador que guany rep una puntuació X i el jugador que en perd la mateixa
   En la ronda de 3X d’un torneig el guanyador rep X, el perdedor perd X i el 2n es queda
  igual.
   En 4X el jugador que queda 1r guanya 2X, el 2n X, el 3r perd X i el darrer perd 2X.
Per tal d’iniciar una partida caldrà disposar del nombre de jugadors necessaris, així doncs
existirà un sistema de cues per tal de gestionar aquesta situació. Quan un jugador cliqui en un
dels 2 modes de joc, aquest entrarà en cua per aquell mode de joc. Des del servidor, s’hauran
d’anar gestionant totes les peticions de partida i en quant hi hagi suficients jugadors per iniciar
una partida crearà aquesta i els eliminarà de la cua. Cal destacar que la cua ha de ser separada
per cada mode de joc. Mentre no es troba partida, s’ha de donar feedback al usuari conforma
s’està esperant a tenir el nombre de jugadors necessari. També, s’ha de poder cancel·lar la cua
si l’usuari es cansa d’esperar tal que aquest serà eliminat de la cua en el servidor i es donarà
feedback en el client.
Cada cop que finalitzi una partida caldrà mostrar la posició en que s’ha quedat així com la
puntuació perduda/guanyada i la puntuació acumulada resultant i a continuació procedir amb
la següent ronda/etapa.
En qualsevol mode de joc, cal mostrar en cada moment el nombre de rondes guanyades per
cada jugador en cada moment de la partida. En un campionat aquests comptadors es
reiniciaran cada cop que un jugador sigui eliminat de la partida. Quan un jugador d’un torneig
és eliminat, aquest no surt de la partida sinó que es queda observant aquesta. Tanmateix, pot
en qualsevol moment triar sortir i deixar observar aquesta. Destacar que en qualsevol mode de
joc es podrà abandonar una partida en qualsevol moment. No obstant, si es fa mentre s’està
jugant una ronda, es penalitzarà a nivell de punts a aquest jugador. En el cas del 2X es tornarà
a cua immediatament donant la partida per guanyada, en el 4X es deixarà finalitzar la ronda i
posteriorment tornarà a cua. En el cas del campionat s’avançarà automàticament d’etapa
sense acabar aquesta.
Per controlar la direcció a la que ha de moure’s el punter o la moto s’utilitzaran les tecles
indicades als paràmetres de configuració. Al principi de cada ronda es farà un compte enrere 
de 3 segons i la partida començarà, moment en el qual es començaran a moure els punters o
motos. El moviment de les motos es permanent, és a dir, no poden estar parades. Sempre
seguiran l’ultima direcció rebuda de l’usuari. Per a canviar de direcció s’haurà de fer mitjançant
una acció de l’usuari, aquest només podrà triar entre 4 direccions (amunt, avall, esquerra i
dreta). Els canvis de direcció sempre es faran en 90 graus, és a dir, si el punter s’està movent
cap amunt, no es podrà girar directament 180 graus cap avall.
El servidor és l’encarregat de rebre els moviments que facin els jugadors i actualitzar
conseqüentment el tauler per tal d’enviar el nou estat de l’escenari als clients connectats. Cal
tenir present que el punter o moto que mouen els jugadors deixa un rastre a mesura que
avança, i que aquest no pot ser travessat per a cap altre punter o moto. El guanyador és qui
aguanta més temps sense tocar una d’aquestes esteles ni cap paret. En el moment en què un
jugador col·lideixi amb una de les línies o amb una paret, aquest perdrà una vida i tornarà a
començar la partida. Aquest procés es repetirà fins que s’acabin les vides d’un dels jugadors.
És important observar que durant el procés de joc, l’estat del tauler del joc ha de ser el mateix
en tots els clients.

3 Consideracions

De forma addicional a les especificacions anteriors, cal tenir en compte les restriccions
següents.

* Estructures de dades

No és necessari que codifiqueu les estructures de dades, com per exemple una llista dinàmica.
Podeu usar les classes del paquet java.util de l'API de Java, com les classes
LinkedList<E>, ArrayList<E>, Vector<E> i/o d'altres.
D'aquesta manera obtindreu, "gratuïtament", estructures de dades amb funcionalitats
interessants, lliures d'errors i ràpides, amb el que agilitzareu molt el procés de codificació de la
pràctica.
  
* Dibuix de gràfiques

Per tal de dibuixar les gràfiques no es pot fer ús de cap llibreria externa al SDK de Java (JDK).
Cal utilitzar algun dels mecanismes que ofereix la llibreria AWT/SWING de Java i escalar els
eixos en funció dels valors màxim i mínim del conjunt de dades que es representen.

* Disseny gràfic

L’aspecte visual tan del programa servidor com client és totalment lliure, sigueu creatius.
Investigueu el potencial que us ofereix Java, utilitzeu menús, separadors, desplegables, colors,
creeu imatges personalitzades pels botons, etcètera.

* Procés de desenvolupament

Abans de posar les mans sobre el teclat i començar a codificar, penseu. Analitzeu què cal
implementar i dissenyeu quines classes i quines relacions tindrà el sistema, agafeu paper i
llapis o utilitzeu l'eina StarUML per tal de crear un diagrama de classes UML base.
Segurament aquest diagrama no serà el definitiu, i anirà canviant a mida que avanceu el
desenvolupament, però d'aquesta manera assentareu unes bases sòlides pel
desenvolupament del sistema i us estalviareu hores de codificació i molts maldecaps.
El desenvolupament d'un projecte informàtic real típicament comprèn les fases: presa de
requeriments, especificació, anàlisi, disseny, implementació, proves i implantació. Podeu
pensar que les dues fases inicials d'aquesta seqüència ja han estat realitzades per l’equip
docent i que ara us passem el relleu per tal que acabeu el sistema, que continueu amb l’anàlisi,
disseny, etcètera.
El no compliment d'algun dels punts especificats en aquest enunciat suposarà la no
acceptació de la pràctica i la devolució de la mateixa.
La detecció de copia comportarà suspendre tots el integrants dels grups implicats,
tant els que han copiat com els que han estat copiats, i perdre l'opció de presentarse
a la pròxima convocatòria ordinària. (veure normativa de la universitat)
