```powershel
#||||||||||||||||||||||||||||||||||||||||||||||
#|||||||||||  Comandos-let para la       ||||||
#               automatización  |||||||||||||||
#|||||||||||||||||| INICIO |||||||||||||||||||| 
       

#  Estableciendo el directorio donde se encuentran los archivos 
#  .csv y que serán cargados al Shell

$rootRPs = "F:\..\"

#  Obteniendo valores de las variables de Excel en formato .csv desde el directorio F

$ruta = (Get-Content -Path $rootRPs\rutaPs.csv -Tail 1);
$ruta = $ruta.Replace("/", "\")
$ruta = $ruta.Replace('"', "")
$unidadPsLag = (Get-Content -Path $rootRPs\unidadPsLag.csv -Tail 1);
$unidadPsLag = $unidadPsLag.Replace('"', "")
$rutaDir = (Get-Content -Path $rootRPs\rutaDirPs.csv -Tail 1);
$rutaDir = $rutaDir.Replace("/", "\")
$rutaDir = $rutaDir.Replace('"', "")
$rutaVar = "F:\..\"
$wwwPs = (Get-Content -Path $rootRPs\wwwPs.csv -Tail 1);
$wwwPs = $wwwPs.Replace('"', "")
$nameDir = "New folder";
$nameProj = "Nuevo proyecto.Rproj"

#  Ajustando los valores obtenidos desde los archivos .csv de Excel

$name = (Get-Content -Path $rootRPs\namePs.csv -Tail 1);
$name = $name.Replace('"', "")
$NewnameProj = $name + ".Rproj"
$unidad = (Get-Content -Path $rootRPs\unidadPs.csv  -Tail 1);
$unidad = $unidad.Replace('"', "")
$message1 = "Volumen de unidad USB actual: "
$message2 = "Cambiar volumen de unidad USB a: "

#  Estableciendo la ruta donde se encuentran los archivos de texto plano,
#  para cambiarles la extensión y puedan ser leídos por el IDE de R.

Set-Location $rutaVar;

Rename-Item Script.txt Script.r;
Rename-Item Markdown.txt Markdown.Rmd; 


#  Moviendo los archivos de R a diferentes directorios

Get-ChildItem  -Path  "Script.r" -Recurse  | Move-Item  -Destination $ruta\$nameDir;
Get-ChildItem  -Path  "Markdown.Rmd" -Recurse  | Move-Item  -Destination $ruta\$nameDir;

#  Renombrando los directorios

Rename-Item -path  $ruta\$nameDir\$nameProj -NewName $NewnameProj
Rename-Item -path  $ruta\$nameDir\imagen -NewName $wwwPs

Set-Location $ruta; 
Rename-Item  $nameDir $name

#  Obteniendo el nombre del pendrive del administrador, 
#  del pendrive actual del usuario y del pendrive anterior del usuario

$unidadUSB_ADMIN = (Get-Location).Drive.Name
$unidadUSB_USER = $unidad
$unidadUSB_USER = $unidadUSB_USER.Replace(":","")
$unidadUSB_USERLag = $unidadPsLag.Replace(":","")

#  Creando un flujo para el contraste de nombres del pendrive,
#  con el objetivo de cambiar el nombre del pendrive actual del usuario, al nombre anterior
#  del pendrive; siempre y cuando, el nombre fuera similar al del pendrive del administrador o diferente del pendrive anterior.
#  De lo contrario se eliminarán algunos archivos y se copiarán otros (reportes), ya sea, en un directorio dentro de la unidad USB del 
#  usuario o en un directorio en la nube.

if ( $unidadUSB_ADMIN -eq $unidadUSB_USER -or $unidadUSB_USERLag -ne $unidadUSB_USER) {
    DiskPart;
    $unidadPsLag = $unidadPsLag.Replace(":","")
    $unidadAct = $unidad.Replace(":","")
    $message1 + $unidadPsLag
    $message2 + $unidadAct
} else {
    Copy-Item -path $ruta\* -Recurse -Exclude "informe.csv"  -Destination $unidad\$name; Set-Location  $unidad\$name; Remove-Item ".Rproj.user"  -r ; Get-ChildItem
} 


#  Iniciando el proceso para ejecutar el archivo Markdown 

start-process $ruta\$name\Markdown.html

#||||||||||||||||||||||||||||||||||||||||||||||
#|||   Fin del proceso de automatización  |||||
#||||||||||||||||||||||||||||||||||||||||||||||   

```
