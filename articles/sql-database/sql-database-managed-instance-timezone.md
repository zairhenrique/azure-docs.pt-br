---
title: Fusos horários Instância Gerenciada
description: Saiba mais sobre as especificações de fuso horário do Instância Gerenciada do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: 0dbed3db8e106b9bfe1b48ff2b9bc52840fc4c3a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818859"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Fusos horários em Instância Gerenciada do Banco de Dados SQL do Azure

UTC (tempo Universal Coordenado) é o fuso horário recomendado para a camada de dados das soluções de nuvem. O Instância Gerenciada do Banco de Dados SQL do Azure também oferece uma escolha de fusos horários para atender às necessidades de aplicativos existentes que armazenam valores de data e hora e funções de data e hora com um contexto implícito de um fuso horário específico.

Funções T-SQL como [GETDATE ()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) ou código CLR observam o fuso horário definido no nível da instância. SQL Server Agent trabalhos também seguem agendas de acordo com o fuso horário da instância.

  >[!NOTE]
  > Instância Gerenciada é a única opção de implantação do banco de dados SQL do Azure que dá suporte à configuração de fuso horário. Outras opções de implantação sempre seguem o UTC.
Use [no fuso horário](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) em bancos de dados SQL únicos e em pool se você precisar interpretar informações de data e hora em um fuso horário não UTC.

## <a name="supported-time-zones"></a>Fusos horários com suporte

Um conjunto de fusos horários com suporte é herdado do sistema operacional subjacente da instância gerenciada. Ele é atualizado regularmente para obter novas definições de fuso horário e refletir as alterações nas existentes.

A [política de horário de verão e alterações de fuso horário](https://aka.ms/time) garante a precisão histórica de 2010 para frente.

Uma lista com nomes dos fusos horários com suporte é exposta por meio da exibição do sistema [Sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) .

## <a name="set-a-time-zone"></a>Definir um fuso horário

Um fuso horário de uma instância gerenciada pode ser definido somente durante a criação da instância. O fuso horário padrão é UTC.

  >[!NOTE]
  > O fuso horário de uma instância gerenciada existente não pode ser alterado.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Definir o fuso horário por meio do portal do Azure

Ao inserir parâmetros para uma nova instância, selecione um fuso horário na lista de fusos horários com suporte.
  
![Definindo um fuso horário durante a criação da instância](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

Especifique a propriedade TimeZoneID em seu [modelo do Resource Manager](https://aka.ms/sql-mi-create-arm-posh) para definir o fuso horário durante a criação da instância.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Uma lista de valores com suporte para a propriedade TimeZoneID está no final deste artigo.

Se não for especificado, o fuso horário será definido como UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Verificar o fuso horário de uma instância

A função [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) retorna um nome de exibição do fuso horário da instância.

## <a name="cross-feature-considerations"></a>Considerações sobre recursos cruzados

### <a name="restore-and-import"></a>Restaurar e importar

Você pode restaurar um arquivo de backup ou importar dados para uma instância gerenciada de uma instância do ou um servidor com configurações de fuso horário diferentes. Certifique-se de fazer isso com cautela. Analise o comportamento do aplicativo e os resultados das consultas e dos relatórios, assim como quando você transfere dados entre duas instâncias de SQL Server com configurações de fuso horário diferentes.

### <a name="point-in-time-restore"></a>Restauração pontual

Quando você executa uma restauração pontual, o tempo para restaurar é interpretado como hora UTC. Dessa forma, quaisquer ambiguidades devido ao horário de verão e suas possíveis alterações são evitadas.

### <a name="auto-failover-groups"></a>Grupos de failover automático

Usar o mesmo fuso horário em uma instância primária e secundária em um grupo de failover não é imposto, mas é altamente recomendável.

  >[!WARNING]
  > É altamente recomendável que você use o mesmo fuso horário para a instância primária e secundária em um grupo de failover. Devido a determinados casos de uso raros, manter o mesmo fuso horário em instâncias primárias e secundárias não é imposta. É importante entender que, no caso de failover manual ou automático, a instância secundária manterá seu fuso horário original.

## <a name="limitations"></a>Limitações

- O fuso horário da instância gerenciada existente não pode ser alterado.
- Os processos externos iniciados por meio dos trabalhos de SQL Server Agent não observam o fuso horário da instância.

## <a name="list-of-supported-time-zones"></a>Lista de fusos horários com suporte

| **ID do fuso horário** | **Nome de exibição do fuso horário** |
| --- | --- |
| Hora padrão do meridiano | (UTC-12:00) International Date Line West |
| UTC-11 | (UTC-11:00) Tempo Universal Coordenado-11 |
| Hora padrão de Aleutas | (UTC-10:00) Ilhas Aleutas |
| Hora oficial do Havaí | (UTC-10:00) Havaí |
| Hora padrão de Marquesas | (UTC-09:30) Ilhas Marquesas |
| Hora padrão do Alasca | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Tempo Universal Coordenado-09 |
| Hora padrão do Pacífico (México) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Tempo Universal Coordenado-08 |
| Hora oficial do Pacífico | (UTC-08:00) Hora do Pacífico (EUA e Canadá) |
| Hora oficial das montanhas dos EUA | (UTC-07:00) Arizona |
| Hora padrão das montanhas (México) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Hora oficial das montanhas | (UTC-07:00) Hora das Montanhas (EUA e Canadá) |
| Hora oficial da América Central | (UTC-06:00) América Central |
| Hora padrão central | (UTC-06:00) Hora Central (EUA e Canadá) |
| Hora oficial da ilha da Páscoa | (UTC-06:00) Ilha de Páscoa |
| Hora padrão central (México) | (UTC-06:00) Guadalajara, México, Monterrey |
| Hora oficial central do Canadá | (UTC-06:00) Saskatchewan |
| Hora padrão de Bogotá | (UTC-05:00) Bogotá, Lima, Quito, Rio Branco |
| Hora padrão do leste (México) | (UTC-05:00) Chetumal |
| Hora oficial do leste | (UTC-05:00) Hora do Leste (EUA e Canadá) |
| Hora padrão do Haiti | (UTC-05:00) Haiti |
| Hora padrão de Cuba | (UTC-05:00) Havana |
| Hora oficial do leste dos EUA | (UTC-05:00) Indiana (Leste) |
| Hora oficial turcos e Caicos | (UTC-05:00) Ilhas Turks e Caicos |
| Hora padrão do Paraguai | (UTC-04:00) Assunção |
| Hora padrão do Atlântico | (UTC-04:00) Horário do Atlântico (Canadá) |
| Hora oficial da Venezuela | (UTC-04:00) Caracas |
| Hora oficial central do Brasil | (UTC-04:00) Cuiabá |
| Hora padrão da SA ocidental | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Hora padrão do Pacífico SA | (UTC-04:00) Santiago |
| Hora padrão de Newfoundland | (UTC-03:30) Terra Nova |
| Hora padrão de Tocantins | (UTC-03:00) Araguaina |
| E. Hora oficial da América do Sul | (UTC-03:00) Brasília |
| Hora oficial da SA Oriental | (UTC-03:00) Caiena, Fortaleza |
| Hora oficial da Argentina | (UTC-03:00) Cidade de Buenos Aires |
| Hora oficial da Groenlândia | (UTC-03:00) Groenlândia |
| Hora padrão de Montevidéu | (UTC-03:00) Montevidéu |
| Hora padrão de Magalhães | (UTC-03:00) Punta Arenas |
| Horário padrão de Saint Pierre | (UTC-03:00) São Pedro e Miquelon |
| Hora padrão de Bahia | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Tempo Universal Coordenado-02 |
| Horário padrão do Atlântico Médio | (UTC-02:00) Atlântico Central – Antiga |
| Hora padrão de Açores | (UTC-01:00) Açores |
| Hora oficial do cabo verde | (UTC-01:00) Cabo Verde |
| UTC | (UTC) Tempo Universal Coordenado |
| Hora padrão GMT | (UTC+00:00) Dublin, Edimburgo, Lisboa, Londres |
| Hora padrão de Greenwich | (UTC+00:00) Monróvia, Reykjavík |
| Mostrar. Hora padrão da Europa | (UTC+01:00) Amsterdã, Berlim, Berna, Roma, Estocolmo, Viena |
| Hora oficial da Europa Central | (UTC+01:00) Belgrado, Bratislava, Budapeste, Liubliana, Praga |
| Hora oficial de romance | (UTC+01:00) Bruxelas, Copenhague, Madri, Paris |
| Hora padrão do Marrocos | (UTC + 01:00) Casablanca |
| Hora padrão de São Tomé | (UTC + 01:00) São Tomé |
| Hora oficial da Europa Central | (UTC+01:00) Sarajevo, Skopje, Varsóvia, Zagreb |
| Mostrar. Hora oficial da África Central | (UTC+01:00) Centro-oeste da África |
| Horário padrão da Jordânia | (UTC+02:00) Amã |
| Hora oficial GTB | (UTC+02:00) Atenas, Bucareste |
| Hora padrão do Oriente Médio | (UTC+02:00) Beirute |
| Hora oficial do Egito | (UTC+02:00) Cairo |
| E. Hora padrão da Europa | (UTC+02:00) Chisinau |
| Hora padrão da Síria | (UTC+02:00) Damasco |
| Hora oficial do banco oeste | (UTC+02:00) Gaza, Hebron |
| Hora padrão da África do Sul | (UTC+02:00) Harare, Pretória |
| Hora oficial FLE | (UTC+02:00) Helsinque, Kiev, Riga, Sófia, Tallinn, Vilnius |
| Hora padrão de Israel | (UTC+02:00) Jerusalém |
| Hora padrão de Caliningrado | (UTC+02:00) Kaliningrado |
| Hora padrão do Sudão | (UTC + 02:00) Cartum |
| Horário padrão da Líbia | (UTC+02:00) Trípoli |
| Hora oficial da Namíbia | (UTC + 02:00) Windhoek |
| Hora oficial árabe | (UTC+03:00) Bagdá |
| Hora padrão da Turquia | (UTC + 03:00) Istambul |
| Hora oficial árabe | (UTC+03:00) Kuwait, Riad |
| Hora padrão de Belarus | (UTC+03:00) Minsk |
| Hora oficial russa | (UTC + 03:00) Moscou, São Petersburgo |
| E. Hora padrão da África | (UTC+03:00) Nairóbi |
| Hora oficial do Irã | (UTC+03:30) Teerã |
| Hora oficial árabe | (UTC+04:00) Abu Dhabi, Mascate |
| Hora padrão de Astrakhan | (UTC+04:00) Astrakhan, Ulyanovsk |
| Hora padrão do Azerbaijão | (UTC+04:00) Baku |
| Zona 3 de tempo da Rússia | (UTC+04:00) Ijevsk, Samara |
| Hora padrão de Maurício | (UTC+04:00) Port Louis |
| Hora padrão de Saratov | (UTC + 04:00) Saratov |
| Hora padrão georgiano | (UTC+04:00) Tbilisi |
| Hora padrão de Volgogrado | (UTC + 04:00) Volgogrado |
| Hora oficial do Cáucaso | (UTC+04:00) Ierevan |
| Hora oficial do Afeganistão | (UTC+04:30) Cabul |
| Hora oficial do oeste da Ásia | (UTC+05:00) Ashgabat, Tashkent |
| Hora padrão de Ecaterimburgo | (UTC+05:00) Ecaterimburgo |
| Horário padrão do Paquistão | (UTC+05:00) Islamabad, Karachi |
| Hora oficial da Índia | (UTC+05:30) Chennai, Kolkata (Calcutá), Mumbai, Nova Délhi |
| Hora oficial do Sri Lanka | (UTC+05:30) Sri Jayawardenepura |
| Hora oficial do Nepal | (UTC+05:45) Katmandu |
| Hora oficial da Ásia Central | (UTC+06:00) Astana |
| Hora padrão de Bangladesh | (UTC+06:00) Dacca |
| Hora padrão de Omsk | (UTC + 06:00) Omsk |
| Hora oficial de Myanmar | (UTC+06:30) Yangon (Rangoon) |
| Hora padrão do Sudeste Asiático | (UTC+07:00) Bancoc, Hanói, Jacarta |
| Hora padrão de Altai | (UTC+07:00) Barnaul, Gorno-Altaysk |
| Mostrar. Hora padrão da Mongólia | (UTC+07:00) Hovd |
| Hora oficial do norte da Ásia | (UTC+07:00) Krasnoyarsk |
| P. Hora oficial da Ásia Central | (UTC + 07:00) Novosibirsk |
| Hora padrão de Tomsk | (UTC+07:00) Tomsk |
| Hora oficial da China | (UTC+08:00) Beijing, Chongqing, Hong Kong, Urumqui |
| Hora padrão do norte da Ásia Oriental | (UTC+08:00) Irkutsk |
| Hora oficial de Cingapura | (UTC+08:00) Kuala Lumpur, Singapura |
| Mostrar. Hora padrão da Austrália | (UTC+08:00) Perth |
| Hora oficial de Taipé | (UTC+08:00) Taipé |
| Hora padrão de Ulan Bator | (UTC+08:00) Ulan Bator |
| Hora padrão da Austrália Ocidental | (UTC+08:45) Eucla |
| Hora padrão de Transbaikal | (UTC+09:00) Chita |
| Hora oficial de Tóquio | (UTC+09:00) Osaka, Sapporo, Tóquio |
| Hora oficial da Coreia do Norte | (UTC + 09:00) Pyongyang |
| Hora padrão da Coreia | (UTC+09:00) Seul |
| Hora padrão de Yakutsk | (UTC+09:00) Yakutsk |
| Central. Hora padrão da Austrália | (UTC+09:30) Adelaide |
| Hora padrão da AUS central | (UTC+09:30) Darwin |
| E. Hora padrão da Austrália | (UTC+10:00) Brisbane |
| Hora oficial da AUS Oriental | (UTC+10:00) Camberra, Melbourne, Sidney |
| Hora oficial do oeste do Pacífico | (UTC+10:00) Guam, Port Moresby |
| Hora oficial da Tasmânia | (UTC+10:00) Hobart |
| Hora padrão de Vladivostok | (UTC+10:00) Vladivostok |
| Horário padrão de Lord Howe | (UTC+10:30) Ilha Lord Howe |
| Hora padrão de Bougainville | (UTC+11:00) Ilha Bougainville |
| Fuso horário de Rússia 10 | (UTC+11:00) Chokurdakh |
| Hora padrão de Magadan | (UTC+11:00) Magadan |
| Hora oficial de Norfolk | (UTC+11:00) Ilha Norfolk |
| Hora padrão de Sakhalin | (UTC+11:00) Sakhalin |
| Hora oficial do Pacífico Central | (UTC+11:00) Ilhas Salomão, Nova Caledônia |
| Fuso horário da Rússia 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Hora oficial da Nova Zelândia | (UTC+12:00) Auckland, Wellington |
| UTC + 12 | (UTC+12:00) Tempo Universal Coordenado+12 |
| Hora padrão de Fiji | (UTC+12:00) Fiji |
| Hora padrão de Kamchatka | (UTC+12:00) Petropavlovsk-Kamchatsky – Antiga |
| Hora padrão das Ilhas Chatham | (UTC+12:45) Ilhas Chatham |
| UTC + 13 | (UTC + 13:00) Tempo universal coordenado + 13 |
| Hora oficial de Tonga | (UTC+13:00) Nuku'alofa |
| Hora oficial de Samoa | (UTC+13:00) Samoa |
| Hora oficial das ilhas de linha | (UTC+14:00) Ilha de Kiritimati |

## <a name="see-also"></a>Confira também 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [NO fuso horário (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys. time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
