---
title: 針對 Azure AD Domain Services 中的安全 LDAP 進行疑難排解 |Microsoft Docs
description: 瞭解如何針對 Azure Active Directory Domain Services 受控網域的安全 LDAP （LDAPS）進行疑難排解
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 96aa463441c9e0f21e2ef1aa27c566b94e1e5f4f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257881"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>針對 Azure Active Directory Domain Services 受控網域的安全 LDAP 連線問題進行疑難排解

使用輕量型目錄存取協定（LDAP）與 Azure Active Directory Domain Services （Azure AD DS）通訊的應用程式和服務，可以[設定為使用安全 LDAP](tutorial-configure-ldaps.md)。 必須開啟適當的憑證和必要的網路埠，安全的 LDAP 才能正常運作。

本文可協助您針對 Azure AD DS 中的安全 LDAP 存取問題進行疑難排解。

## <a name="common-connection-issues"></a>常見的連接問題

如果您無法使用安全 LDAP 連線到 Azure AD DS 受控網域，請參閱下列疑難排解步驟。 在每個疑難排解步驟之後，再次嘗試連接到 Azure AD DS 受控網域：

* 安全 LDAP 憑證的簽發者鏈結在用戶端上必須受信任。 您可以將根憑證授權單位（CA）新增至用戶端上的受信任根憑證存放區，以建立信任。
    * 請確定您已[匯出憑證，並將其套用至用戶端電腦][client-cert]。
* 確認受控網域的安全 LDAP 憑證具有 [主旨 *] 或 [* *主體替代名稱*] 屬性中的 DNS 名稱。
    * 請檢查[安全的 LDAP 憑證需求][certs-prereqs]，並視需要建立取代憑證。
* 確認 LDAP 用戶端（例如*ldp.exe* ）使用 DNS 名稱（而非 IP 位址）連接到安全的 ldap 端點。
    * 套用至 Azure AD DS 受控網域的憑證不會包含服務的 IP 位址，而只包括 DNS 名稱。
* 檢查 LDAP 用戶端連線到的 DNS 名稱。 它必須解析為 Azure AD DS 受控網域上安全 LDAP 的公用 IP 位址。
    * 如果 DNS 名稱解析為內部 IP 位址，請更新 DNS 記錄以解析為外部 IP 位址。
* 針對外部連線能力，網路安全性群組必須包含允許從網際網路到 TCP 埠636流量的規則。
    * 如果您可以從直接連線到虛擬網路但不是外部連線的資源，使用安全的 LDAP 連接到 Azure AD DS 受控網域，請務必[建立網路安全性群組規則，以允許安全的 ldap 流量][ldaps-nsg]。

## <a name="next-steps"></a>後續步驟

如果您仍有問題，請[開啟 Azure 支援要求][azure-support]以取得額外的疑難排解協助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
