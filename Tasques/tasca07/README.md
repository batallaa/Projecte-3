# T07: Instal·lant un servidor de noms

## Breu descripció

Després de l’exitosa experiència a nivell de formació, els nostres clients de Digicore estan tan satisfets amb la nostra feina que ens encarreguen la implantació des de zero dels seus serveis de DNS interns.

Actualment, l'agència fa servir adreces IP per accedir als seus servidors de desenvolupament, bases de dades i eines de gestió interna. 

Aquest mètode és ineficient i propens a errors:

- **Usabilitat deficient:** Els empleats han de memoritzar o buscar constantment adreces IP complexes (p. ex., 192.168.10.25).

- **Manteniment feixuc:** Si un servidor canvia la seva IP, cal notificar i actualitzar manualment la configuració a tots els equips i aplicacions que s'hi connecten.

- **Manca de professionalitat:** En un entorn professional, tots els serveis haurien de ser accessibles mitjançant noms fàcils de recordar.

Per tant, la nostra missió és implementar un Sistema de Noms de Domini (DNS) intern robust.

L'objectiu és que els servidors i aplicacions de l'agència es puguin accedir utilitzant noms de domini amigables (p. ex., bbdd.digicore.lan o wiki.digicore.lan).

![ex01](img/ex01.png)

## El vostre repte

La recomanació com a consultora és utilitzar BIND9, l'estàndard de facto de servidor de noms a Linux, per la seva fiabilitat i flexibilitat.

La vostra missió serà:

- Instal·lar i configurar un servidor DNS primari (màster) amb BIND9 en un sistema Linux.

- Crear una Zona Directa (Forward Zone) i una Zona Inversa (Reverse Zone) per al domini privat de DigiCore.

Garantir que:

- els noms es resolguin a IPs,

- les IPs es resolguin a noms.

Per fer una prova de concepte, utilitzareu el domini:

**digicore-XX.test**

on XX serà el vostre número de llista.

## Pas previ

Configurar un Ubuntu Server amb:

- 4 GB de RAM

- 20 GB de disc

Aquesta màquina virtual haurà de tenir:

- Una interfície en adaptador pont (la configuració s’indicarà a l’inici del repte).

- Una segona interfície en host-only.

**Un cop configurat:**

- Instal·lar el paquet bind9.

- Instal·lar el servei ssh al servidor, per poder exportar els arxius de configuració al vostre repositori de GitHub.

## Accions a realitzar

**1. Configurar named.conf.options**

- Acceptar consultes recursives de la xarxa local.

- Utilitzar com a reenviador la IP 8.8.8.8.

- Mostrar la captura.

- Reiniciar el servei i comprovar que funciona (mostrar l’estat).

**2. Configurar un client**

Podeu fer servir la màquina Zorin usada anteriorment, canviant l’adaptador a adaptador pont.
La configuració s’indicarà a l'inici del repte, però el servidor de noms (DNS) ha de ser la IP del vostre servidor.

- Comprovar que el client té resolució a Internet:

- Obrir una pàgina al navegador, o

- Fer un dig google.com.

**3. Configurar zones al named.conf.local**

**Definir dues zones:**

- Zona directa del domini digicore-XX.test

- Zona inversa de la xarxa local usada en la prova de concepte

**4. Crear l’arxiu de la Zona Directa**

- Crear la carpeta /etc/bind/zones.

- Crear l’arxiu de la zona directa (podeu copiar db.local).

- Configurar els registres:

- SOA (amb les dades correctes)

- NS que sigui el vostre servidor

**Registre A:**

- server → IP del servidor

- dbserver → IP del client

**Registre CNAME:**

- data → dbserver

**5. Crear l’arxiu de la Zona Inversa**

- Crear l’arxiu dins /etc/bind/zones (podeu copiar db.127).

**Configurar:**

- SOA i NS adients

**Registres PTR per:**

- server

- dbserver

Reiniciar el servei i fer comprovacions des del client amb consultes directes i inverses.

**6. Transferència de zona**

- Editar named.conf.local per permetre la transferència de la zona directa als companys de l’equip.

- Configurar una zona secundària del domini d’un dels companys.

- Forçar la transferència.

- Comprovar el funcionament des del client.

**Activitat d’avaluació del repte**

Per demostrar la vostra competència tècnica, haureu de superar una avaluació pràctica al final del repte.

Durant aquesta avaluació només podreu usar com a material de suport un full manuscrit amb les vostres anotacions, que haureu de lliurar en finalitzar la prova.
