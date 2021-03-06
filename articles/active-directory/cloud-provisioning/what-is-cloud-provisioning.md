---
title: O que é Azure AD Connect provisionamento de nuvem. | Microsoft Docs
description: Descreve Azure AD Connect provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69b8cbdba018b9d03dca67573b1f4fe6efa546b6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024034"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>O que é o provisionamento em nuvem do Azure AD Connect?
Azure AD Connect provisionamento em nuvem é um novo agente da Microsoft projetado para atender e atingir suas metas de identidade híbrida para a sincronização de usuários, grupos e contatos com o Azure AD.  Ele pode ser usado junto com Azure AD Connect sincronização e oferece os seguintes benefícios:
    
- Suporte para sincronização para um locatário do Azure AD de um ambiente de floresta Active Directory desconectado de várias florestas: os cenários comuns incluem a fusão & aquisição, em que as florestas do AD da empresa adquiridas são isoladas do AD da empresa pai florestas e empresas que têm historicamente várias florestas do AD.
- Instalação simplificada com agentes de provisionamento leves: os agentes atuam como uma ponte do AD para o Azure AD, com todas as configurações de sincronização gerenciadas na nuvem. 
- Vários agentes de provisionamento podem ser usados para simplificar implantações de alta disponibilidade, especialmente críticas para as organizações que dependem da sincronização de hash de senha do AD para o Azure AD.


![O que é o Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Como Azure AD Connect o provisionamento de nuvem diferente do Azure AD Connect sincronização?
Com o provisionamento Azure AD Connect Cloud, o provisionamento do AD para o Azure AD é orquestrado no Microsoft Online Services. Uma organização só precisa implantar, em seu ambiente local e de IaaS, um agente leve que atue como uma ponte entre o Azure AD e o AD. A configuração de provisionamento é armazenada no Azure AD e gerenciada como parte do serviço.

A tabela a seguir fornece uma comparação entre Azure AD Connect e Azure AD Connect o provisionamento de nuvem:

| Recurso | Sincronização de Azure Active Directory Connect| Provisionamento Azure Active Directory Connect nuvem |
|:--- |:---:|:---:|
|Conectar-se a única floresta do AD local|● |● |
| Conectar-se a várias florestas do AD local |● |● |
| Conectar-se a várias florestas locais desconectadas do AD | |● |
| Modelo de instalação do agente leve | |● |
| Vários agentes ativos para alta disponibilidade | |● |
| Conectar-se a diretórios LDAP|●| | 
| Suporte para objetos de usuário |● |● |
| Suporte para objetos de grupo |● |● |
| Suporte para objetos de contato |● |● |
| Suporte para objetos de dispositivo |● | |
| Permitir personalização básica para fluxos de atributo |● |● |
| Atributos do sychronize Exchange Online |● |● |
| Sincronizar atributos de extensão 1-15 |● |● |
| Sincronizar atributos do AD definidos pelo cliente (extensões de diretório) |● | |
| Suporte para sincronização de hash de senha |●|●|
| Suporte para autenticação de passagem |●||
| Suporte para Federação |●|●|
| Logon Único Contínuo|● |●|
| Oferece suporte à instalação em um controlador de domínio |● |● |
| Suporte para o Windows Server 2012 e Windows Server 2012 R2 |● |● |
| Filtrar em domínios/UOs/grupos |● |● |
| Filtrar os valores de atributo de objetos |● | |
| Permitir que um conjunto mínimo de atributos seja sincronizado (MinSync) |● |● |
| Permitir a remoção de atributos do fluxo do AD para o AD do Azure |● |● |
| Permitir a personalização avançada para fluxos de atributo |● | |
| Suporte para Write-back (senhas, dispositivos, grupos) |● | |
| Suporte a Azure AD Domain Services|● | |
| [Write-back híbrido do Exchange](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Suporte para mais de 50.000 objetos por domínio do AD |● | |

## <a name="next-steps"></a>Próximos passos 

- [O que é provisionamento?](what-is-provisioning.md)
- [Instalar o provisionamento de nuvem](how-to-install.md)
