`

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



# Consultar miembros de lista dinámica

```powershell
# 1. Guardamos la lista en una variable
$listaDinamica = Get-DynamicDistributionGroup -Identity "Nombre_de_la_lista"

# 2. Le pedimos a Exchange que nos dé la vista previa según su filtro
Get-Recipient -RecipientPreviewFilter $listaDinamica.RecipientFilter | Select-Object Name, PrimarySmtpAddress
```
# Consultar miembros de lista estática

```powershell
Get-DistributionGroupMember -Identity "Nombre_de_tu_lista" | Select-Object Name, PrimarySmtpAddress
```
