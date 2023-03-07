Voici un détail des fonctions autorisés, dans mon cas j'ai utilisé webserv, le dernier exam utilise (select) mais c'est quasiment le même principe.

# Fonctions autorisées

 - Everything in C++ 98.
 - `htons`, `htonl`, `ntohs`, `ntohl` : htonl, htons, ntohl, ntohs - convert values between host and network byte order [man](https://linux.die.net/man/3/htons)
   > Network byte order is big-endian, host byte order is little-endian. Therefore, both ntohl and htonl return a byte-swapped version of their input. [source](https://stackoverflow.com/a/11423368/)

    https://stackoverflow.com/questions/15863775/ntohl-vs-htonl-in-little-endian  
    Si je comprends ce que je l'ai certaine fois on devra convertir l'ip (ou une valeur qui s'en rapproche?) de l'un à l'autre
 - `select`:
   > Les fonctions `select()` et `pselect()` permettent à un programme de surveiller plusieurs descripteurs de fichier, attendant qu'au moins l'un des descripteurs de fichier devienne « prêt » pour certaines classes d'opérations d'entrées-sorties (par exemple, entrée possible). Un descripteur de fichier est considéré comme prêt s'il est possible d'effectuer l'opération d'entrées-sorties correspondante (par exemple, un [read](http://manpagesfr.free.fr/man/man2/read.2.html)(2)) sans bloquer. [source](http://manpagesfr.free.fr/man/man2/select.2.html)
     
    (J'ai [vérifié ref stackoverflow](https://stackoverflow.com/questions/13378035/socket-and-file-descriptors) car j'avais un doute: ) Les socket sont des file descriptors.
<details>
  <summary>Linux only</summary>

**/!\ Mac OS X doesn't support epoll, but it does support [kqueue](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man2/kqueue.2.html) which is very similar.** [ref](https://stackoverflow.com/a/4039853/)
  
 - `poll`
   > `poll()` est une variation de [select](http://manpagesfr.free.fr/man/man2/select.2.html)(2) : il attend que l'un des descripteurs de fichier parmi un ensemble soit prêt pour effectuer des entrées-sorties.  
   > L'ensemble des descripteurs de fichier à surveiller est indiqué dans l'argument fds qui est un tableau de structures nfds [...]. [source](http://manpagesfr.free.fr/man/man2/poll.2.html)
     
    [What are the differences between poll and select?](https://stackoverflow.com/questions/970979/what-are-the-differences-between-poll-and-select) Apparemment il faudra utiliser poll au lieu de select (mais ça c'est une réponse pour du C++17 mais je pense que c'est pareil pour du std98.
 - `epoll` (`epoll_create`, `epoll_ctl`, `epoll_wait`): epoll - Notifications d'événements d'entrées-sorties.
   > epoll est une variante de [poll](http://manpagesfr.free.fr/man/man2/poll.2.html)(2) que l'on peut déclencher par niveau ou par changement d'état, et monte bien en charge pour un grand nombre de descripteurs simultanés. Trois appels système sont fournis pour configurer et commander un ensemble epoll [source](http://manpagesfr.free.fr/man/man7/epoll.7.html)
     
    [select v.s. poll v.s. epoll](https://hechao.li/2022/01/04/select-vs-poll-vs-epoll/)
</details>

 - `kqueue` (`kqueue`, `kevent`) 
   > `kqueue()` provides a generic method of notifying the user when an event happens or a condition holds, based on the results of small pieces of kernel code termed “filters”. A kevent is identified by the (ident, filter) pair; there may only be one unique kevent per kqueue. [source](https://man.openbsd.org/kqueue.2)  
   
   Tuto/wiki: [kqueue tutorial](https://wiki.netbsd.org/tutorials/kqueue_tutorial/) + explication de la gestion du timeout :)
   Article: [Handling TCP Connections with Kqueue Event Notification](http://eradman.com/posts/kqueue-tcp.html)
 - `socket`: Créer un point de communication [man](http://manpagesfr.free.fr/man/man2/socket.2.html)
   [Socket Programming in C/C++](https://www.geeksforgeeks.org/socket-programming-cc/) <== **SCHÉMA INTÉRESSANT**  
   [Learning Socket Programming in C++](https://www.codingninjas.com/blog/2020/07/06/learning-socket-programming-in-c/)  
   [Sockets - Server & Client - 2020](https://www.bogotobogo.com/cplusplus/sockets_server_client.php)   

   ![image](https://user-images.githubusercontent.com/92152391/191218824-6c560606-d705-4c7f-8691-24e02811ec14.png)  

 - `accept`: Accepter une connexion sur une socket  
   > L'appel système `accept()` est employé avec les sockets utilisant un protocole en mode connecté (SOCK_STREAM et SOCK_SEQPACKET). Il extrait la première connexion de la file des connexions en attente de la socket sockfd à l'écoute, crée une nouvelle socket connectée, et renvoie un nouveau descripteur de fichier qui fait référence à cette socket. La nouvelle socket n'est pas en état d'écoute. La socket originale sockfd n'est pas modifiée par l'appel système. [source](http://manpagesfr.free.fr/man/man2/accept.2.html)
 - `listen`:
   > `listen()` marque la socket référencée par sockfd comme une socket passive, c'est-à-dire comme une socket qui sera utilisée pour [accept](http://manpagesfr.free.fr/man/man2/accept.2.html)er les demandes de connexions entrantes en utilisant accept(2). [source](http://manpagesfr.free.fr/man/man2/listen.2.html)
 - `send`: Envoyer un message sur une socket
   > Les appels système send(), sendto(), et sendmsg() permettent de transmettre un message à destination d'une autre socket. L'appel send() ne peut être utilisé qu'avec les sockets connectées (ainsi, le destinataire visé est connu). La seule différence entre send() et [write](http://manpagesfr.free.fr/man/man2/write.2.html)(2) est la présence de flags. Sans aucun paramètre flags, send() est équivalent à [write](http://manpagesfr.free.fr/man/man2/write.2.html)(2). De plus, send(s,buf,len,flags) est équivalent à sendto(s,buf,len,flags,NULL,0). [source](http://manpagesfr.free.fr/man/man2/send.2.html)
 - `recv`: Recevoir un message sur une socket
   > L'appel `recv()` est normalement utilisé sur une socket connectée (voir [connect](http://manpagesfr.free.fr/man/man2/connect.2.html)(2)) et est équivalent à `recvfrom()` avec un paramètre from NULL. [source](http://manpagesfr.free.fr/man/man2/recv.2.html)
 - `bind`: Fournir un nom à une socket  
   > Quand une [socket](http://manpagesfr.free.fr/man/man2/socket.2.html) est créée avec l'appel système socket(2), elle existe dans l'espace des noms mais n'a pas de nom assigné). bind() affecte l'adresse spécifiée dans addr à la socket référencée par le descripteur de fichier sockfd. addrlen indique la taille, en octets, de la structure d'adresse pointée par addr. Traditionnellement cette opération est appelée « affectation d'un nom à une socket ».  
    > Il est normalement nécessaire d'affecter une adresse locale avec bind() avant qu'une socket SOCK_STREAM puisse recevoir des connexions (voir [accept](http://manpagesfr.free.fr/man/man2/accept.2.html)(2)). [source](http://manpagesfr.free.fr/man/man2/bind.2.html)
    
    Pour nous (en ipv4, si on suit la doc de bind), nous devons suivre; https://man7.org/linux/man-pages/man7/ip.7.html
 - `connect`: Débuter une connexion sur une socket  
   > L'appel système `connect()` connecte la socket référencée par le descripteur de fichier sockfd à l'adresse indiquée par serv_addr. L'argument addrlen indique la taille de serv_addr. Le format de l'adresse dans serv_addr est déterminé par l'espace adresse de la socket sockfd ; voir [socket](http://manpagesfr.free.fr/man/man2/socket.2.html)(2) pour plus de détails. [source](http://manpagesfr.free.fr/man/man2/connect.2.html)
 - `inet_addr`: Routines de manipulation d'adresses Internet  
   > La fonction `inet_addr()` convertit l'adresse Internet de l'hôte cp depuis la notation numérique pointée IPV4 en une donnée binaire dans l'ordre des octets du réseau. Si l'adresse est invalide, INADDR_NONE (généralement -1) est renvoyé. L'utilisation de cette fonction pose problème car -1 est une adresse valide (255.255.255.255). Évitez-la au profit de inet_aton(), [inet_pton](http://manpagesfr.free.fr/man/man3/inet_pton.3.html)(3) ou [getaddrinfo](http://manpagesfr.free.fr/man/man3/getaddrinfo.3.html)(3) qui fournissent un renvoi d'erreur plus propre. [source](http://manpagesfr.free.fr/man/man3/inet.3.html)
 - `setsockopt`: Lire et écrire les options d'une socket.
   > `getsockopt()` et `setsockopt()` manipulent les options associées à une socket. Ces options peuvent exister aux divers niveaux du protocole, et sont toujours présentes au niveau socket le plus élevé. [source](http://manpagesfr.free.fr/man/man2/getsockopt.2.html)  

   On peut modifier la valeur d'une option en indiquant son nom, qu'on peut trouver ici:
     - https://pubs.opengroup.org/onlinepubs/000095399/functions/setsockopt.html
     - https://learn.microsoft.com/en-us/windows/win32/api/winsock/nf-winsock-setsockopt
 - `getsockname`: Obtenir le nom d'une socket  
   > `getsockname()` renvoie le nom name de la socket indiquée. Le paramètre namelen doit être initialisé pour indiquer la taille de la zone mémoire pointée par name. En retour, il contiendra la taille effective (en octets) du nom renvoyé. [source](http://manpagesfr.free.fr/man/man2/getsockname.2.html)  
    Je n'ai pas trop compris l'intérêt de bind un nom à la socket
 - `fcntl`: Manipuler un descripteur de fichier  
   > fcntl() permet de se livrer à l'une des opérations décrites plus loin sur le descripteur de fichier fd. L'opération est déterminée par la valeur de l'argument cmd. [source](http://manpagesfr.free.fr/man/man2/fcntl.2.html)

# Exemples complets

- https://dev.to/frevib/a-tcp-server-with-kqueue-527
- https://stackoverflow.com/questions/25228938/tcp-server-workers-with-kqueue
- Links in kqueue section of this message (tuto + article)
