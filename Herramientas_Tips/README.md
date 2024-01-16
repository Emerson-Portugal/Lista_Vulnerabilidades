# Herramitas y Tips de Hacking

Aqui te voy a mencionar las Herramientas y Tips que vas utililzar a lo largo de una Auditoria Web 


## Diccionario de Kali Linux


**Recursos de Diccionario** ([Tool Page](https://www.kali.org/tools/seclists/))

```bash
sudo apt -y install seclists
```

## Agregamos la nueva ruta URL - IP a `/etc/hosts`

1. Agregamos la nueva ruta encontrada a `/etc/hosts`

```bash
sudo nano /etc/hosts 
```

2. Agregamos y Guardamos 

```bash
127.0.0.1       localhost
127.0.1.1       kali
10.10.11.242    devvortex.htb dev.devvortex.htb
```


## Conecion remora aun Maquina (Hack the Box)

```zsh

└─$ whatweb 10.10.11.242

http://10.10.11.242 [302 Found] Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.10.11.242], RedirectLocation[http://devvortex.htb/], Title[302 Found], nginx[1.18.0]
ERROR Opening: http://devvortex.htb/ - no address for devvortex.htb

```

Parece que el servidor web está redirigiendo a `http://devvortex.htb/`, pero hay un error al intentar abrir esa dirección **(ERROR Opening: http://devvortex.htb/ - no address for devvortex.htb),** lo que indica que el nombre de dominio `devvortex.htb` no puede resolverse.

- Solucion

Para agregar manualmente una entrada al archivo /etc/hosts y asociar devvortex.htb con la dirección IP 10.10.11.242, sigue estos pasos:

1. Abre una terminal

```bash
sudo nano /etc/hosts
```

2. Añade la línea al archivo hosts:

```bash
10.10.11.242    devvortex.htb
```

3. Guarda y cierra el archivo:

```bash
cat /etc/hosts
```

> Después de realizar estos pasos, deberías poder acceder al sitio web http://devvortex.htb/ en tu navegador y se debería resolver correctamente a la dirección IP 10.10.11.242.



## Nmap

Esta herramienta nos va a permitir ver los puertos abiertos y servicios que esta usando el serviddor Web


1. Con Archivo 

    ```bash
    nmap -p- -sV -sV --open -sS -vvv -n -Pn "IP" -on Archivo
    ```


2. Sin Archivo 

    ```bash
    nmap -p- -sV -sV --open -sS -vvv -n -Pn "IP"
    ```


- **-p-:** Escanea todos los puertos (del 1 al 65535). Este parámetro indica a Nmap que realice un escaneo de todos los puertos.

- **-sV:** Detección de versión. Intenta determinar las versiones de los servicios que se están ejecutando en los puertos abiertos.

- **--open:** Muestra solo los puertos abiertos en la salida. Reduce la salida solo a la información sobre puertos que están en un estado "abierto".

- **-sS:** Escaneo de tipo SYN. Utiliza el escaneo SYN (Stealth Scan) para determinar el estado de los puertos sin completar la conexión.

- **-vvv:** Nivel de verbosidad. Indica un nivel alto de verbosidad. Muestra información detallada durante el escaneo.

- **-n:** No resuelve nombres de hosts durante el escaneo. Evita la resolución DNS y muestra las direcciones IP en lugar de nombres de host.

- **-Pn:** No realiza el descubrimiento de hosts y trata la(s) IP(s) dada(s) como en línea. Ignora la comprobación de la disponibilidad de hosts.


> Nota: **verbosidad**, se refiere al nivel de detalle y cantidad de información que se muestra durante y después del escaneo. Cuanto más alto es el nivel de verbosidad, más información detallada se muestra en la salida.

## Whatweb

Es una herramienta de escaneo de aplicaciones web que se utiliza para identificar tecnologías y plataformas específicas que se ejecutan en un servidor web. 

```bash
whatweb "IP"
```

#### Salida

```bash
└─$ whatweb 10.10.11.242       

http://10.10.11.242 [302 Found] Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.10.11.242], RedirectLocation[http://devvortex.htb/], Title[302 Found], nginx[1.18.0]
http://devvortex.htb/ [200 OK] Bootstrap, Country[RESERVED][ZZ], Email[info@DevVortex.htb], HTML5, HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.10.11.242], JQuery[3.4.1], Script[text/javascript], Title[DevVortex], X-UA-Compatible[IE=edge], nginx[1.18.0]

```


## FFuF (Fuzz Faster U Fool)

Es una herramienta que automatiza el proceso de búsqueda de rutas, archivos, directorios y otros recursos en una aplicación web mediante la generación de solicitudes HTTP personalizadas.


**Comando**

```bash
ffuf -u http://URL/ -H "Host:FUZZ.URL" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -fs 154
```

**Ejemplo**

```bash
ffuf -u http://devvortex.htb/ -H "Host:FUZZ.devvortex.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -fs 154
```

**Salida**

```bash

┌──(kali㉿kali)-[~]
└─$ ffuf -u http://devvortex.htb/ -H "Host:FUZZ.devvortex.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -fs 154

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.3.1 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://devvortex.htb/
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
 :: Header           : Host: FUZZ.devvortex.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response size: 154
________________________________________________

dev                     [Status: 200, Size: 23221, Words: 5081, Lines: 502]
[WARN] Caught keyboard interrupt (Ctrl-C)

```




## Gobuster 

Su función principal es enumerar y descubrir directorios y archivos ocultos en un sitio web mediante la generación de solicitudes HTTP.


- **vhost:** Uses VHOST enumeration mode (you most probably want to use the IP address as the URL parameter)

**Comando**

```bash
gobuster vhost -u "URL" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt 

```

**Ejemplo**

```bash
gobuster vhost -u devvortex.htb -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt 

```

**Salida**

```bash
┌──(kali㉿kali)-[~]
└─$ gobuster vhost -u devvortex.htb -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:             http://devvortex.htb
[+] Method:          GET
[+] Threads:         10
[+] Wordlist:        /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
[+] User Agent:      gobuster/3.6
[+] Timeout:         10s
[+] Append Domain:   false
===============================================================
Starting gobuster in VHOST enumeration mode
===============================================================
Progress: 6023 / 114442 (5.26%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 6046 / 114442 (5.28%)
===============================================================
Finished
===============================================================

```



- **dir:** Uses directory/file enumeration mode

**Comando**

```bash
gobuster dir -u "URL" -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20

```

**Ejemplo**

```bash
gobuster dir -u dev.devvortex.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20

```

**Salida**

```bash

┌──(kali㉿kali)-[~]
└─$ gobuster dir -u dev.devvortex.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://dev.devvortex.htb
[+] Method:                  GET
[+] Threads:                 20
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/images/]
/home                 (Status: 200) [Size: 23221]
/media                (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/media/]
/templates            (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/templates/]
/modules              (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/modules/]
/plugins              (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/plugins/]
/includes             (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/includes/]
/language             (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/language/]
/components           (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/components/]
/api                  (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/api/]
/cache                (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/cache/]
/libraries            (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/libraries/]
Progress: 2440 / 220561 (1.11%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 2445 / 220561 (1.11%)
===============================================================
Finished
===============================================================
                                                                   
```



