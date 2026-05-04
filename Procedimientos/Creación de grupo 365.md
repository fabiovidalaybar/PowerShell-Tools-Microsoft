```powershell
Connect-ExchangeOnline -UserPrincipalName tu-usuario@tu-dominio.com
```

Este es solo el comando para crear el grupo
```powershell
New-UnifiedGroup -DisplayName "Nombre del Grupo" `
                 -Alias "alias-del-grupo" `
                 -EmailAddresses "correo-del-grupo@tu-dominio.com" `
                 -AccessType "Private" `
                 -Owner "admin@tu-dominio.com"
```
Con este código puedes crear el grupo añadiendo multiples usuarios que deben estar previamente cargados en un archivo .csv
```powershell
Creamos el nuevo grupo de Microsoft 365
$nombreGrupo = "Nombre de tu Grupo"
$aliasGrupo = "alias-del-grupo"

Write-Host "Creando el grupo $nombreGrupo..." -ForegroundColor Cyan
New-UnifiedGroup -DisplayName $nombreGrupo `
                 -Alias $aliasGrupo `
                 -AccessType "Private" `
                 -Owner "tu-admin@tudominio.com"

# Le damos unos segundos a Microsoft para que procese la creación del grupo
Start-Sleep -Seconds 10 

# 3. Importamos el archivo CSV que preparaste
$listaUsuarios = Import-Csv -Path "C:\usuarios.csv"

# 4. Agregamos a los usuarios uno por uno con mucho cuidado
Write-Host "Agregando usuarios al grupo..." -ForegroundColor Cyan

foreach ($usuario in $listaUsuarios) {
    try {
        # Usamos el alias del grupo y la columna "Correo" de tu CSV
        Add-UnifiedGroupLinks -Identity $aliasGrupo -LinkType Members -Links $usuario.Correo
        Write-Host "¡Añadido con éxito!: $($usuario.Correo)" -ForegroundColor Green
    }
    catch {
        Write-Host "Hubo un problemita al agregar a: $($usuario.Correo)" -ForegroundColor Red
    }
}

Write-Host "¡Todo listo! Proceso terminado." -ForegroundColor Magenta


```
# 5. Cerramos la sesión
```powershell
Disconnect-ExchangeOnline -Confirm:$false
```
