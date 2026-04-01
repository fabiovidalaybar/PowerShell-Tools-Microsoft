```powershell

```

## Utilidades
### Comandos Base

Variables:  
**Tipo String**  

> [!IMPORTANT]
> Las comillas simples y dobles cambian el contexto, ojito con eso.


$nombre_de_la_variable = "fabio"  
Ejemplo:  
$nombre = "Fabio"  
$mensaje = "Hola $nombre"   
El resultado será: Hola Fabio    
$nombre = "Fabio"    
$mensaje = 'Hola $nombre'     
El resultado será: Hola $nombre (literalmente)

**Tipo Lista**    
$nombre_de_la_variable = @(1, 2, 3, “A”)    
