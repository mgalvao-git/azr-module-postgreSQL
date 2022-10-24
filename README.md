# Modulo - Azure PostgreSQL Services (PostgreSQL)
[![COE](https://img.shields.io/badge/Created%20By-CCoE-blue)]()
[![HCL](https://img.shields.io/badge/language-HCL-blueviolet)](https://www.terraform.io/)
[![Azure](https://img.shields.io/badge/provider-Azure-blue)](https://registry.terraform.io/providers/hashicorp/azurerm/latest)

Modulo desenvolvido com o objetivo de padronizar a criação dos databases Postgresql.

## Compatibilidade de Versão

| Versão do Modulo | Versão Terraform | Versão AzureRM |
|----------------|-------------------| --------------- |
| v1.0.0       | v1.2.9| 3.25.0        |

## Especificando versão

Para evitar que seu código receba atualizações automáticas do modulo, é preciso informar o parâmetro `version` do modulo com a versão desejada, conforme pode ser visto no exemplo abaixo.

source                 = "git::https://namecia@dev.azure.com/namecia/Projeto_IaC/_git/azr-module-postgresql"

version                = "v1.0.0"

## Exemplo de uso

```hcl


module "postgresql-nameproject-fqa" {

  source                 = "git::https://namecia@dev.azure.com/namecia/Projeto_IaC/_git/azr-module-postgresql?ref=v1.0.0"

# Pgsql flexible server

  name                   = "postgresql-nameproject-fqa"
  location               = "brazilsouth"
  rg_name                = "rg-br-itfqa-fqa-nameproject"
  storage_mb             = 32768
  postgresql_version     = 14
  sku_name               = "B_Standard_B2s"
  delegated_subnet_id    = "/subscriptions/numberid/resourceGroups/rg-br-itfqa-shared-network/providers/Microsoft.Network/virtualNetworks/vnet-brsouth-fqa/subnets/snet-brsouth-fqa-sql-001"      ### Confirmar a subnet que irá configurar o banco  ###
  private_dns_zone_id    = "/subscriptions/numberid/resourceGroups/rg-br-itnetworkshared-shared-network/providers/Microsoft.Network/privateDnsZones/privatelink.postgres.database.azure.com"        ### DNS Específico para o postgres  ###
  zone = 1
  backup_retention_days  = 7 
  high_availability = { valores = {mode = "ZoneRedundant" 
  standby_availability_zone = "2"}}    #### O HA Não está disponível para a região do Brasil. ####
  tags = {
    department = "<ti|engenharia|oss>"
    area = "<infrastructure|magistratura|big data>"
    project = "<id do portal infra>"
    system = "<OCS|BSCS|ECM|Siebel|Meu project>"
    dl_owner = "<dl owner>@namecia.com.br"
    environment = "<prd|dev|fqa|dr|sh|preprd|poc|contigencia>" 
  } 

# Pgsql databases

  db_names     =  [ "postgresqldb-nameproject-fqa01", "postgresqldb-nameproject-fqa02" ]
  db_charset                    = "utf8" 
  db_collation                  = "en_US.utf8" 
}

```

## Entrada de Valores

| Nome | Descrição | Tipo | Padrão | Requerido |
|------|-------------|------|---------|:--------:|
| name | nome que será dado ao recusro | `string` | n/a | Sim |
| location | região no Azure onde o arquivo será criado | `string` | n/a | Sim |
| rg_name | nome do resource group onde os recursos serão alocados | `string` | n/a | Sim |
| private_dns_zone_id | nome dado para o prefixo cadastrado no private dns | `string` | `mesmo nome do cluster` | Não |
| postgresql_version |  Versão do PostgreSQL Flexible Server. Possiveis valores são : https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_flexible_server#version. | `number` | `14` | Não |
| sku_name | Nome do tier SKU, Exemplo: B_Standard_B1ms, GP_Standard_D2s_v3, MO_Standard_E4s_v | `string` | B_Standard_B1ms | Não |
| zone | Zona específica para o PostgreSQL Flexible principal | `number` | 1 | Não |
| tags | Tags adicionais | `map(any)` | `{}` | Não |
| administrator_login |	PostgreSQL Login Administrativo | `string`	|n/a |	Sim |
| administrator_password	| PostgreSQL Senha Administrador.: https://docs.microsoft.com/en-us/sql/relational-databases/security/strong-passwords?view=sql-server-2017.|	string	|n/a	| Sim |
| backup_retention_days	| Retenção do Backup (Entre 7 e 35 dias).	| number	| 7	| Sim |
| db_charset	| PostgreSQL charset Valido : https://www.postgresql.org/docs/current/multibyte.html#CHARSET-TABLE	| map(string)	|{} |	Não |
| db_collation |	PostgreSQL collation Valido : http://www.postgresql.cn/docs/13/collation.html - be careful about https://docs.microsoft.com/en-us/windows/win32/intl/locale-names?redirectedfrom=MSDN	| map(string)	|{}	| Não |
| db_names	| Nome para o database |	list(string)	|n/a	| Sim |
| delegated_subnet_id	| Id da subnet para criar o PostgreSQL Flexible Server. (Não deve ser qualquer recurso deployed in)	| string	| null	| Não |
| storage_mb | Storage Habilitado PostgresSQL Flexible server. Valores Possíveis : https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_flexible_server#storage_mb. |	number	|32768	| Não | 
! standby_zone | Especifica alta disponibilidade para o PostgreSQL Flexible Server. (Null fica desabilitado o high-availability) |	number	| 2 | Não |


## Saída de Valores

| Nome | Descrição |
|------|-------------|
| postgresql_flexible_database_ids | O mapa de todos os recursos de identificação do database |
| postgresql_flexible_databases_names	| o mapa com os nomes dos databases. |
| postgresql_flexible_server_id	| Servidor PostgreSQL ID.

## Documentação de Referência

Terraform Azure Postgresql Flexible Server Services: <br>
[https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_flexible_server](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_flexible_server)<br>

Terraform Azure Postgresql Flexible Server Database Services: <br>
[https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_flexible_server_database](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_flexible_server_database)<br>

Random Password: <br>
[https://registry.terraform.io/providers/hashicorp/random/latest/docs/resources/password](https://registry.terraform.io/providers/hashicorp/random/latest/docs/resources/password)<br>
