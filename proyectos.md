## Práctica del tema: crear un formulario que permita introducir nombres y operación (crear usuario, grupo, OU, etc.) al departamento de Recursos Humanos, cada vez que se introduce un usuario se añade a un fichero JSON que se sube a un servidor web, por otro lado, desarrollar un script que lea ese fichero JSON desde una URL y realice las operaciones que están indicadas (crear usuario, grupo, OU, etc.) además para cada usuario crear un código QR

```powershell

using assembly System.Windows.Forms
using namespace System.Windows.Forms
 
class Operacion
{ 
  [Int]$operacion
  [String]$nombre

 
    #Constructor de la clase
    Operacion($operacion,$nombre)
    {
        $this.operacion = $operacion
        $this.nombre = $nombre
    }
}
 
# Crear array de objetos Operacion
$arrayoperaciones = New-Object System.Collections.ArrayList
 
$form = [Form] @{
 Text = 'Mi formulario'
}
 
$TextBox = [TextBox] @{
 Location = New-Object System.Drawing.Size(100,80)
}
$TextBox2 = [TextBox] @{
 Location = New-Object System.Drawing.Size(100,120)
}
 
$button = [Button] @{
 Text = 'Pulsar'
 Location = New-Object System.Drawing.Size(100,180)
}
$button.add_Click{
 Write-Host $TextBox.Text
 $arrayoperaciones.add([Operacion]::new($TextBox.Text,$TextBox2.Text))
}
 

$form.Controls.Add($TextBox)
$form.Controls.Add($TextBox2)
$form.Controls.Add($button)
$form.ShowDialog()



$arrayoperaciones | ConvertTo-Json | Out-File operacion.txt  -Encoding default

notepad .\operacion.txt


$json = Get-Content .\operacion.txt

foreach($usuarios in Invoke-RestMethod "http://localhost/dashboard/operacion.txt"){
    
    if ($usuarios.operacion -match "1")
    {
    $password = (ConvertTo-SecureString "Pa`asswd2" -asplaintext -force)
    New-ADUser -name $usuarios.nombre -sam $usuarios.nombre -Path "OU=alumnos, DC=andel, DC=local" -AccountPassword $password -enable $true
    }
    elseif ($usuarios.operacion -match "2")
    {
        Remove-ADUser $usuarios.nombre 
    
    }
}

```

--- 

## Práctica sobre Administración de procesos del sistema: Comparar el hash de un programa utilizando mysql

```powershell

[void][System.Reflection.Assembly]::LoadWithPartialName("MySql.Data")
$Connection = New-Object MySql.Data.MySqlClient.MySqlConnection
$ConnectionString = "server=" + "localhost" + ";port=3306;uid=" + "root" + ";pwd=" + ";database="+"test"
$Connection.ConnectionString = $ConnectionString
$Connection.Open()

foreach($proceso in (Get-Process).path)
{
   $var= (get-filehash $proceso).path.split("\")[-1]
   $var2 = (get-filehash $proceso).hash
   $Query='INSERT INTO ASO (nombre,hash) VALUES ("'+$var+'","'+$var2+'")'
    $Command = New-Object MySql.Data.MySqlClient.MySqlCommand($Query, $Connection)
    $DataAdapter = New-Object MySql.Data.MySqlClient.MySqlDataAdapter($Command)
    $DataSet = New-Object System.Data.DataSet
    $RecordCount = $dataAdapter.Fill($dataSet, "data")
    $DataSet.Tables[0]
    $Connection.Close()

}

$var = foreach($proceso in (Get-Process).path)
{
    (get-filehash $proceso).path.split("\")[-1]

}

```

---



