# T04: Serveis de directori. LDAP
**Carlos Alonso**

SMX 2B

## Índex

- **Part 1:** Instal·lació del servei LDAP. **3** 
- **Part 2:** Configuració de directoris. **7** 
- **Part 3:** Autenticació amb LDAP. **13**

---

## Part 1: Instal·lació del servei LDAP

Canviem el nom de hostname de la nostra màquina:  

`sudo hostnamectl set-hostname server.innovatech04.test`

![Captura 1](img/imatge1.png)

Instal·lem el servei i les utilitats LDAP.

`apt install slapd ldap-utils -y`

![Captura 2](img/imatge2.png)

Se’ns obrirà aquest menú. Possarem contrasenya al slapd. En el meu cas utilitzaré “usuari” per evitar que se m’oblidi.

![Captura 3](img/imatge3.png)

Comprovem després que el slapd està actiu.

`sudo systemctl status slapd`

![Captura 4](img/imatge4.png)

Després de veure que el servei slapd està actiu, obrirem la configuració un altre cop per donar un nom a la organització.

`dpkg-reconfigure slapd`

Omitim la configuració del servidor OpenLDAP.

![Captura 5](img/imatge5.png)

Configurarem el nom de la nostra organització. En el meu cas, haig de posar innovatechxx.test

![Captura 6](img/imatge6.png)

Fem que es borri la anterior base de dades.

![Captura 7](img/imatge7.png)

Movem la anterior base de dades a un backup.

![Captura 8](img/imatge8.png)

**Creació de OU (usuari, grup)**

Editem els arxius **OU_users.ldif** i **OU_groups.ldif** per poder-los agregar a ldap. Per això, haurem de fer la comanda que mostraré a continuació i escriure segons el domini que tinguis la següent informació:

`sudo nano OU_users.ldif / OU_groups.ldif`

![Captura 9](img/imatge9.png)
![Captura 10](img/imatge10.png)

Després, els anyadim amb la següent comanda. Ens demanarà password de LDAP.

`sudo ldapadd -D “cn=admin,dc=innovatechxx,dc=test” -W -f OU_users.ldif / OU_groups.ldif`

![Captura 11](img/imatge11.png)
![Captura 12](img/imatge12.png)

Després de crear-les, fem un ldapsearch per veure si s’han creat correctament.

`ldapsearch -xLLL -b “dc=innovatechxx,dc=test”`

![Captura 13](img/imatge13.png)

I podem filtrar-ho perquè només ens mostri els users i grups creats.

`ldapsearch -xLLL -b “dc=innovatechxx,dc=test” objectClass=organizationalUnit`

![Captura 14](img/imatge14.png)

Amb ldapdelete podem eliminar.

---

## Part 2: Configuració de directoris.

Instal·lem el paquet de gestor.

`sudo apt install ldap-account-manager -y`

![Captura 15](img/imatge15.png)

Utilitzant la ip de **Host-Only** (amb ip a la podem veure) i /lam al final podem entrar al gestor. Pressionem LAM configuration per poder modificar.

![Captura 16](img/imatge16.png)

Pressionem la segona opció per poder editar els perfils del servidor.

![Captura 17](img/imatge17.png)

Per iniciar sessió, posarem el password que en el meu cas és “lam”, i si volem modificar els nostres perfils li donarem a l’opció d’abaix.

![Captura 18](img/imatge18.png)

Configurem la llista d'usuaris vàlids, on posarem l’usuari admin i de domini innovatech i test. **(cn=admin,dc=innovatech,dc=test)**.

![Captura 19](img/imatge19.png)

Canviem l’idioma predeterminat i la zona horària de la màquina.

![Captura 20](img/imatge20.png)

Al sufix de l’arbre posarem els dos dominis de la màquina; innovatech i test **(dc=innovatech,dc=test)**.

![Captura 21](img/imatge21.png)

Configurem els sufixes dels usuaris i grups.

![Captura 22](img/imatge22.png)

Al haver configurat tot això, iniciem sessió amb el password de la màquina **“p@ssw0rd”** i comencem a fer les modificacions dels usuaris i grups.

![Captura 23](img/imatge23.png)

Anem a crear un grup: cuentas → grupos.

![Captura 24](img/imatge24.png)

Crearem dos grups: **manager i tech**. El número GID es posa automàticament.

![Captura 25](img/imatge25.png)
![Captura 26](img/imatge26.png)

Creem dos usuaris: tech01 i manager01. 

![Captura 27](img/imatge27.png)
![Captura 28](img/imatge28.png)

Hem de editar el seu directori perquè es faci una carpeta personal amb el seu nom d’usuari.

![Captura 29](img/imatge29.png)
![Captura 30](img/imatge30.png)

I posarem una contrasenya a cada un dels dos usuaris. Pot ser la que vulguis, en el meu cas **“1234”**.

![Captura 31](img/imatge31.png)

---

## Part 3: Autenticació amb LDAP.

Crearem una màquina Zorin i posarem dos interfícies: **NAT i Host-Only**.

![Captura 32](img/imatge32.png)

A la terminal de Zorin, editem l’arxiu de hosts per posar el nom del client, i posarem la seva IP.

`sudo nano /etc/hosts`

![Captura 33](img/imatge33.png)
![Captura 34](img/imatge34.png)

Podem fer hostname -f al acabar per comprovar que és correcte.

![Captura 35](img/imatge35.png)

Fem una snapshot desde el VisualBox per si fem un error (si no ho fem, possiblement haurem de reinstal·lar la màquina client).

![Captura 36](img/imatge36.png)

Instal·larem els mòduls necessaris per poder usar libpam i nss.

`sudo apt install libnss-ldap libpam-ldap ldap-utils nscd -y`

![Captura 37](img/imatge37.png)

Modifiquem l’identificador del servidor, posant el hostname de la màquina servidor.

![Captura 38](img/imatge38.png)

Posem el nom dels dos dominis: dc=innovatechxx,dc=test

![Captura 39](img/imatge39.png)

Seleccionem la versió LDAP que volem utilitzar, tot i que no influeix en ninguna configuració posterior.

![Captura 40](img/imatge40.png)

Posem que sigui admin.

![Captura 41](img/imatge41.png)

Fem la base de dades de LDAP no requereixi d’un login.

![Captura 42](img/imatge42.png)

Introduïm la nostra conta LDAP d’admin:
**cn=admin,dc=innovatechxx,dc=test**

![Captura 43](img/imatge43.png)

I per finalitzar, introduïm una contrasenya per **l’administrador LDAP**.

![Captura 44](img/imatge44.png)

Des del client, fem una consulta ldap al servidor per si es connecta.

`sudo ldapsearch -x -d ‘cn=admin,dc=innovatechxx,dc=test -W -H ldap://server.innovatechxx.test -b ‘dc=innovatechxx,dc=test’ ObjectClass=posixAccount uid`

![Captura 45](img/imatge45.png)

Configurem l’arxiu nsswitch per indicar que s’utilitzarà ldap per passwords, grups i contrasenyes. 

`sudo nano /etc/nsswitch.conf`

![Captura 46](img/imatge46.png)

A l’arxiu /etc/pan.d/common_password eliminem de la línea indicada el terme use_authtok

`sudo nano /etc/pan.d/common-password`

![Captura 47](img/imatge47.png)

Ara obrim l’arxiu /etc/pam.d/common-session i afegim una última línea abaix de tot per indicar la creació de perfils.

`sudo nano /etc/pam.d/common-session`

![Captura 48](img/imatge48.png)

Després d’haver modificat els arxius anteriors, reiniciem el nscd.

`systemctl restart nscd`

![Captura 49](img/imatge49.png)

Comprovem que veu els usuaris creats amb ldap

`getent passwd | tail`

![Captura 50](img/imatge50.png)

I per finalitzar, obrim l’arxiu /etc/pam.d/gdm-launch-environment per permetre el inici de sessió en interfície gràfica.

`sudo nano /etc/pam.d/gdm-launch-environment`

![Captura 51](img/imatge51.png)

Reiniciem el client i comprovem que ens deixa entrar amb una de les comptes ldap creades.

![Captura 52](img/imatge52.png)

