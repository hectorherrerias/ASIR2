## Práctica del tema: crear un formulario que permita introducir nombres y operación (crear usuario, grupo, OU, etc.) al departamento de Recursos Humanos, cada vez que se introduce un usuario se añade a un fichero JSON que se sube a un servidor web, por otro lado, desarrollar un script que lea ese fichero JSON desde una URL y realice las operaciones que están indicadas (crear usuario, grupo, OU, etc.) además para cada usuario crear un código QR

`powershell`

using assembly System.Windows.Forms
using namespace System.Windows.Forms

class Operacion
{
    [Int]$operacion
    [String]$nombre

    # Contructor de la clase
    Operacion($operacion, $nombre)
    {
        $this.operacion = $operacion
        $this.nombre = $nombre
    
    }

}

# Crear array de objetos de Operacion

$arrayoperaciones = New-Object System.Collections.ArrayList

$form = [form] @{
 Text = 'Mi formulario'
 }

 $TextBox =[TextBox] @{
  Location = New-Object System.Drawing.Size(100,80)
  }
  $TextBox2 = [TextBox] @{
  Location = New-Object System.Drawing.Size(100,80)
  }

  $button = [Button] @{
  Text = 'Pulsar'
  Location = New-Object System.Drawing.Size(100,180)
  }
  $button.add_Click{
  Write-Host $TextBox.Text
  $arrayoperaciones.Add([Operacion]::new($TextBox.Text,$TextBox2.Text))
  }

  $form.Controls.Add($TextBox)
  $form.Controls.Add($TextBox2)
  $form.Controls.Add($button)
  $form.ShowDialog()


  $arrayoperaciones | ConvertTo-Json | Out-File opeacion.txt -Encoding default

  $json = Get-Content .\operacion.txt

  foreach($usuario in Invoke-RestMethod "http://localhost/dashboard/operacion.txt"){

        if($usuario.operacion -match "1"){
        $password = (ConvertTo-SecureString "Pa´sswd2" -AsPlainText -force)
        New-ADuser -name $usuario.nombre -sam $usuario.nombre -Path "OU=alumnos, DC=andel DC=local"
        }
        elseif ($usuario.operacion -match "2"){
        Remove-ADuser $usuario.nombre
        }
}