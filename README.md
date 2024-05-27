# script-em-PowerShell-para-lista-de-programas-no-seu-pc
Este script em PowerShell coleta uma lista de todos os programas instalados no seu computador Windows e salva essa lista em um arquivo de texto chamado InstalledPrograms.txt na raiz do drive C:.
Estrutura do Código
Função para listar programas instalados via Windows Installer (MSI)

powershell
Copiar código
function Get-InstalledPrograms {
    Get-WmiObject -Class Win32_Product | Select-Object -Property Name, Version
}
Get-InstalledPrograms: Esta função utiliza o Get-WmiObject para acessar o Windows Management Instrumentation (WMI) e obter uma lista de programas instalados pelo Windows Installer (MSI).
Select-Object -Property Name, Version: Seleciona apenas os campos Name (Nome do Programa) e Version (Versão) para cada programa encontrado.
Função para listar programas instalados via chaves de registro Uninstall

powershell
Copiar código
function Get-UninstallPrograms {
    $uninstallKeys = @(
        "HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*",
        "HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*",
        "HKCU:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*"
    )

    $programs = @()
    foreach ($key in $uninstallKeys) {
        $programs += Get-ItemProperty $key | Select-Object -Property DisplayName, DisplayVersion
    }
    return $programs
}
Get-UninstallPrograms: Esta função examina certas áreas do Registro do Windows onde os programas instalados geralmente são listados.
$uninstallKeys: Contém os caminhos das chaves de registro que armazenam informações sobre os programas instalados.
foreach ($key in $uninstallKeys): Para cada chave de registro na lista, obtém as propriedades DisplayName (Nome Exibido) e DisplayVersion (Versão Exibida) de cada programa.
Obter e combinar as listas de programas

powershell
Copiar código
$installedPrograms = Get-InstalledPrograms
$uninstallPrograms = Get-UninstallPrograms
$installedPrograms: Armazena a lista de programas obtida pela função Get-InstalledPrograms.
$uninstallPrograms: Armazena a lista de programas obtida pela função Get-UninstallPrograms.
Unir as listas e remover duplicatas

powershell
Copiar código
$allPrograms = $installedPrograms + $uninstallPrograms | Sort-Object -Property DisplayName -Unique
$allPrograms: Combina as duas listas ($installedPrograms e $uninstallPrograms), ordena os programas pelo nome (DisplayName) e remove entradas duplicadas.
Filtrar apenas programas com nomes definidos

powershell
Copiar código
$allPrograms = $allPrograms | Where-Object { $_.DisplayName -ne $null }
Where-Object { $_.DisplayName -ne $null }: Filtra a lista para incluir apenas os programas que têm um DisplayName definido (não nulo).
Exibir a lista de programas

powershell
Copiar código
$allPrograms | Format-Table -Property DisplayName, DisplayVersion -AutoSize
Format-Table -Property DisplayName, DisplayVersion -AutoSize: Formata a lista de programas em uma tabela legível no console do PowerShell, mostrando o nome e a versão de cada programa.
Salvar a lista em um arquivo de texto

powershell
Copiar código
$allPrograms | Out-File -FilePath "C:\InstalledPrograms.txt"
Out-File -FilePath "C:\InstalledPrograms.txt": Salva a lista de programas em um arquivo de texto localizado em C:\InstalledPrograms.txt.
Mensagem de confirmação

powershell
Copiar código
Write-Host "A lista de programas instalados foi salva em C:\InstalledPrograms.txt"
Write-Host: Exibe uma mensagem no console do PowerShell confirmando que a lista foi salva no local especificado.
Conclusão
Este script coleta informações sobre todos os programas instalados no seu computador, combinando dados do Windows Installer (MSI) e do Registro do Windows, remove duplicatas, e salva a lista resultante em um arquivo de texto para fácil referência.
