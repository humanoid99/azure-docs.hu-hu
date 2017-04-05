# Áttekintés
## [Mi az az Azure Active Directory?](active-directory-whatis.md)
## [Kiadás kiválasztása](active-directory-editions.md)
## [Tudnivalók az Azure-identitáskezelésről](fundamentals-identity.md)
## [Az Azure AD-portál felületének előzetese](active-directory-preview-explainer.md)


# Bevezetés
## [Azure AD-bérlő beszerzése](active-directory-howto-tenant.md)
## [Azure AD Premium-fiók regisztrálása](active-directory-get-started-premium.md)
## [Azure-előfizetések társítása](active-directory-how-subscriptions-associated-directory.md)
## Az Azure AD-licencek kezelése
### [Azure Portal](active-directory-licensing-get-started-azure-portal.md)
### [Klasszikus portál](active-directory-licensing-what-is.md)
## [Azure beszerzése a szervezet számára](sign-up-organization.md)
## [Gyakori kérdések](active-directory-faq.md)
## [A SaaS-alkalmazás oktatóanyagai](active-directory-saas-tutorial-list.md)

# Útmutató
## Tervezés és kialakítás
### [Hibrid identitáskezelési megoldás üzembe helyezése](active-directory-hybrid-identity-design-considerations-overview.md)
#### Követelmények meghatározása
##### [Identitáskezelés](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Címtár-szinkronizálás](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Többtényezős hitelesítés](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Az identitás-életciklus stratégiája](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Az adatbiztonság tervezése](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Adatvédelem](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Tartalomkezelés](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Hozzáférés-vezérlés](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Incidensmegoldás](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Az identitás-életciklus megtervezése
##### [Feladatok](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Bevezetési stratégia](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Következő lépések](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Eszközök összehasonlítása](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Felhasználók kezelése
### Felhasználók hozzáadása
#### [Azure Portal](active-directory-users-create-azure-portal.md)
#### [Klasszikus portál](active-directory-create-users.md)

### [Más címtárakban szereplő felhasználók felvétele (klasszikus portál)](active-directory-create-users-external.md)
### [Felhasználók törlése](active-directory-users-delete-user-azure-portal.md)
### [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
### [Új jelszó létrehozása](active-directory-users-reset-password-azure-portal.md)
### [A felhasználók munkahelyi adatainak kezelése](active-directory-users-work-info-azure-portal.md)
### [Fiókok megosztása](active-directory-sharing-accounts.md)

## [Csoportok és tagok kezelése](active-directory-manage-groups.md)
### Csoportok kezelése
#### [Azure Portal](active-directory-groups-create-azure-portal.md)
#### [Klasszikus portál](active-directory-accessmanagement-manage-groups.md)
#### [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Csoporttagok kezelése](active-directory-groups-members-azure-portal.md)
### [Csoporttulajdonosok kezelése](active-directory-accessmanagement-managing-group-owners.md)
### [Csoporttagság kezelése](active-directory-groups-membership-azure-portal.md)
### [Az összes csoport megtekintése](active-directory-groups-view-azure-portal.md)
### [Dedikált csoportok engedélyezése](active-directory-accessmanagement-dedicated-groups.md)
### [Csoportszintű hozzáférés biztosítása az SaaS-alkalmazásokhoz](active-directory-accessmanagement-group-saasapps.md)
### Csoportbeállítások kezelése
#### [Azure Portal](active-directory-groups-settings-azure-portal.md)
#### [Parancsmagok](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Speciális szabályok létrehozása
#### [Azure Portal](active-directory-groups-dynamic-membership-azure-portal.md)
#### [Klasszikus portál](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [Csoportalapú licencelés](active-directory-licensing-whatis-azure-portal.md)
#### [Licencek hozzárendelése egy csoporthoz](active-directory-licensing-group-assignment-azure-portal.md)
#### [A csoportok licencproblémáinak azonosítása és megoldása](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre](active-directory-licensing-group-migration-azure-portal.md)
#### [További forgatókönyvek csoportalapú licenceléshez](active-directory-licensing-group-advanced.md)
### [Önkiszolgáló csoportok beállítása](active-directory-accessmanagement-self-service-group-management.md)
### [Hibaelhárítás](active-directory-accessmanagement-troubleshooting.md)

## [Jelentések kezelése](active-directory-reporting-azure-portal.md)
### [Bejelentkezési tevékenység](active-directory-reporting-activity-sign-ins.md)
### [Naplózási tevékenység](active-directory-reporting-activity-audit-logs.md)
### [Veszélyeztetett felhasználók](active-directory-reporting-security-user-at-risk.md)
### [Kockázatos bejelentkezések](active-directory-reporting-security-risky-sign-ins.md)
### [Kockázati események](active-directory-reporting-risk-events.md)
### [Nevesített hálózatok](active-directory-known-networks-azure-portal.md)
### [Jelentések migrálása](active-directory-reporting-migration.md)
### [Megőrzés](active-directory-reporting-retention.md)
### [Gyakori kérdések](active-directory-reporting-faq.md)
### Hibaelhárítás
#### [Hiányzó naplózási adatok](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Hiányzó adatok a letöltésekben](active-directory-reporting-troubleshoot-missing-data-download.md)
###    Szoftveres hozzáférés
#### [Naplózási referencia](active-directory-reporting-api-audit-reference.md)
#### [Naplózási minták](active-directory-reporting-api-audit-samples.md)
#### [Előfeltételek](active-directory-reporting-api-prerequisites.md)
#### [Bejelentkezési referencia](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Bejelentkezési minták](active-directory-reporting-api-sign-in-activity-samples.md)
### [Klasszikus portál](active-directory-view-access-usage-reports.md)
#### [Azure AD Reporting](active-directory-reporting-getting-started.md)
#### [Jelentéskészítési útmutató](active-directory-reporting-guide.md)
#### [Ismert hálózatok](active-directory-known-networks.md)
#### [API](active-directory-reporting-api-getting-started.md)
#### [Események naplózása](active-directory-reporting-audit-events.md)
#### [Késések](active-directory-reporting-latencies.md)
#### [Értesítések](active-directory-reporting-notifications.md)
#### A jelentések értelmezése
##### [Szabálytalan bejelentkezés](active-directory-reporting-irregular-sign-in-activity.md)
##### [Több hiba](active-directory-reporting-sign-ins-after-multiple-failures.md)
##### [Gyanús IP-címek](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
##### [Különböző földrajzi régiók](active-directory-reporting-sign-ins-from-multiple-geographies.md)
##### [Potenciálisan fertőzött eszközök](active-directory-reporting-sign-ins-from-possibly-infected-devices.md)
##### [Ismeretlen források](active-directory-reporting-sign-ins-from-unknown-sources.md)
##### [Rendellenes bejelentkezések](active-directory-reporting-users-with-anomalous-sign-in-activity.md)

## [Jelszavak kezelése](active-directory-manage-passwords.md)
### [Saját jelszó frissítése](active-directory-passwords-update-your-own-password.md)
### [Önkiszolgáló jelszó-visszaállítás beállítása](active-directory-passwords.md)
### [A jelszókezeléssel kapcsolatos tudnivalók](active-directory-passwords-how-it-works.md)
### [A szabályzatokkal és a korlátozásokkal kapcsolatos tudnivalók](active-directory-passwords-policy.md)
### Új jelszavak létrehozása
#### [Azure Portal](active-directory-users-reset-password-azure-portal.md)
#### [Klasszikus portál](active-directory-create-users-reset-password.md)
### [Elévülési szabályzatok beállítása](active-directory-passwords-set-expiration-policy.md)
### Jelszókezelés engedélyezése
#### [Első lépések](active-directory-passwords-getting-started.md)
#### [Üzembe helyezés](active-directory-passwords-best-practices.md)
#### [Testreszabás](active-directory-passwords-customize.md)
#### [Jelentések megtekintése](active-directory-passwords-get-insights.md)
#### [További információ](active-directory-passwords-learn-more.md)
#### [Gyakori kérdések](active-directory-passwords-faq.md)
#### [Hibaelhárítás](active-directory-passwords-troubleshoot.md)

## Eszközök kezelése
### [Eszközök regisztrálása](active-directory-device-registration-overview.md)
#### [Telepítés](active-directory-conditional-access-automatic-device-registration-setup.md)
#### [Helyszíni üzembe helyezés](active-directory-device-registration-on-premises-setup.md)
#### [Gyakori kérdések](active-directory-device-registration-faq.md)
#### Hibaelhárítás
##### [Hibaelhárítás Windows 10 és Windows Server 2016 esetén](active-directory-device-registration-troubleshoot-windows.md)
##### [Hibaelhárítás korábbi verziójú Windows-ügyfelek esetén](active-directory-device-registration-troubleshoot-windows-legacy.md)
### [Azure AD-csatlakozás](active-directory-azureadjoin-overview.md)
#### [Csomag](active-directory-azureadjoin-deployment-aadjoindirect.md)
#### [Az eszközregisztráció beállítása](active-directory-azureadjoin-setup.md)
#### [Új eszközök regisztrálása](active-directory-azureadjoin-user-frx.md)
#### [Üzembe helyezés](active-directory-azureadjoin-devices-group-policy.md)
#### [A Windows 10-es integrációval kapcsolatos tudnivalók](active-directory-azureadjoin-windows10-devices-overview.md)
#### [Windows 10-eszközök használata](active-directory-azureadjoin-windows10-devices.md)
#### [Eszköz csatlakoztatása](active-directory-azureadjoin-personal-device.md)
#### [Windows 10-es eszköz csatlakoztatása](active-directory-azureadjoin-user-upgrade.md)

## Alkalmazások kezelése
### [Áttekintés](active-directory-enable-sso-scenario.md)
### [Első lépések](active-directory-integrating-applications-getting-started.md)

### [Felhőalkalmazások felderítése](active-directory-cloudappdiscovery-whatis.md)
#### [A beállításjegyzék-beállítások frissítése](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
#### [A biztonsággal és adatvédelemmel kapcsolatos tudnivalók](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)

### [Távoli hozzáférés biztosítása az alkalmazásokhoz](active-directory-application-proxy-get-started.md)
#### [Alkalmazásproxy engedélyezése](active-directory-application-proxy-enable.md)
#### [Az összekötők ismertetése](application-proxy-understand-connectors.md)

#### Alkalmazások közzététele
##### [Azure Portal](application-proxy-publish-azure-portal.md)
##### [Klasszikus portál](active-directory-application-proxy-publish.md)

#### [Biztonság](application-proxy-security-considerations.md)
#### [Hálózatok](application-proxy-network-topology-considerations.md)
#### [Távoli asztal](application-proxy-publish-remote-desktop.md)
#### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)

#### Közzététel külön hálózatokon
##### [Azure Portal](active-directory-application-proxy-connectors-azure-portal.md)
##### [Klasszikus portál](active-directory-application-proxy-connectors.md)
#### [Proxykiszolgálók](application-proxy-working-with-proxy-servers.md)
#### [Egyéni tartományok](active-directory-application-proxy-custom-domains.md)
#### [Hozzáférés az alkalmazásokhoz](active-directory-appssoaccess-whatis.md)
##### [Azure Portal](application-proxy-sso-azure-portal.md)
#### [Egyszeri bejelentkezés KCD-vel](active-directory-application-proxy-sso-using-kcd.md)
#### [Egyszeri bejelentkezés fejlécekkel](application-proxy-ping-access.md)
#### [Jogcímbarát alkalmazások](active-directory-application-proxy-claims-aware-apps.md)
#### [Natív ügyfélalkalmazások](active-directory-application-proxy-native-client.md)
#### [Egyéni kezdőlap](application-proxy-office365-app-launcher.md)
#### [Feltételes hozzáférés](active-directory-application-proxy-conditional-access.md)
#### [Csendes telepítés](active-directory-application-proxy-silent-installation.md)
#### [Microsoft Forefront](application-proxy-transition-from-uag-tmg.md)
#### [Hibaelhárítás](active-directory-application-proxy-troubleshoot.md)

### Vállalati alkalmazások kezelése
#### [Felhasználók hozzárendelése](active-directory-coreapps-assign-user-azure-portal.md)
#### [Márkajelzés testreszabása](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [Felhasználói bejelentkezések letiltása](active-directory-coreapps-disable-app-azure-portal.md)
#### [Felhasználók eltávolítása](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Az összes alkalmazás megtekintése](active-directory-coreapps-view-azure-portal.md)
#### [Felhasználói fiók üzembe helyezésének kezelése](active-directory-enterprise-apps-manage-provisioning.md)

### Fejlesztés
#### [Felhasználók hozzárendelése](active-directory-applications-guiding-developers-assigning-users.md)
#### [Csoportok hozzárendelése](active-directory-applications-guiding-developers-assigning-groups.md)
#### [Hozzárendelés igénylése](active-directory-applications-guiding-developers-requiring-user-assignment.md)
#### [LoB-alkalmazások fejlesztése](active-directory-applications-guiding-developers-for-lob-applications.md)

### [Alkalmazások hozzáférésének kezelése](active-directory-managing-access-to-apps.md)
#### [Önkiszolgáló hozzáférés](active-directory-self-service-application-access.md)
#### [Egyszeri bejelentkezés tanúsítványai](active-directory-sso-certs.md)
#### [Bérlőkorlátozások](active-directory-tenant-restrictions.md)

### [SCIM használata a felhasználók átadására](active-directory-scim-provisioning.md)
### [Dokumentumtár](active-directory-apps-index.md)

## Címtár kezelése
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Egyéni tartománynevek
#### [Áttekintés](active-directory-add-domain-concepts.md)
#### Tartománynév hozzáadása
##### [Azure Portal](active-directory-domains-add-azure-portal.md)
##### [Klasszikus portál](active-directory-add-domain.md)
##### [AD FS használatával](active-directory-add-domain-federated.md)
#### [Felhasználók hozzárendelése](active-directory-add-domain-add-users.md)
#### Tartománynevek kezelése
##### [Azure Portal](active-directory-domains-manage-azure-portal.md)
##### [Klasszikus portál](active-directory-add-manage-domain-names.md)
### A bejelentkezési oldal testreszabása
#### [Azure Portal](active-directory-branding-custom-signon-azure-portal.md)
#### [Nyelvspecifikus](active-directory-branding-localize-azure-portal.md)
#### [Klasszikus portál](active-directory-add-company-branding.md)
### [A címtár felügyelete](active-directory-administer.md)
### [Több címtár](active-directory-licensing-directory-independence.md)
### [O365-címtárak](active-directory-manage-o365-subscription.md)
### [Önkiszolgáló regisztráció](active-directory-self-service-signup.md)
### [Vállalati állapothordozás](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Bekapcsolás](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Csoportházirend-beállítások](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10-beállítások](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Gyakori kérdések](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Hibaelhárítás](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

### [Partnerek integrálása az Azure AD B2B-vel](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [B2B-felhasználók rendszergazdák általi hozzáadása](active-directory-b2b-admin-add-users.md)
#### [B2B-felhasználók infomunkások általi hozzáadása](active-directory-b2b-iw-add-users.md)
#### [Meghívó e-mail](active-directory-b2b-invitation-email.md)
#### [Meghívó beváltása](active-directory-b2b-redemption-experience.md)
#### [B2B-felhasználó tulajdonságai](active-directory-b2b-user-properties.md)
#### [Vendégfelhasználó hozzáadása szerepkörhöz](active-directory-b2b-add-guest-to-role.md)
#### [Naplózás és jelentéskészítés](active-directory-b2b-auditing-and-reporting.md)
#### [API és testreszabás](active-directory-b2b-api.md)
#### [Meghívók delegálása](active-directory-b2b-delegate-invitations.md)
#### [Dinamikus csoportok és B2B](active-directory-b2b-dynamic-groups.md)
#### [Többtényezős hitelesítés B2B esetén](active-directory-b2b-mfa-instructions.md)
#### [B2B-felhasználói jogkivonatok](active-directory-b2b-user-token.md)
#### [B2B-felhasználói jogcímek társítása](active-directory-b2b-claims-mapping.md)
#### [Külső Office 365-megosztás](active-directory-b2b-o365-external-user.md)
#### [SaaS-appok konfigurálása B2B esetén](active-directory-b2b-configure-saas-apps.md)
#### [Programkód- és PowerShell-minták](active-directory-b2b-code-samples.md)
#### [Aktuális korlátozások](active-directory-b2b-current-limitations.md)
#### [Licencek](active-directory-b2b-licensing.md)
#### [B2B-hibaelhárítás](active-directory-b2b-troubleshooting.md)
#### [B2B-együttműködés összehasonlítása B2C-vel](active-directory-b2b-compare-b2c.md)
#### [Támogatás igénybevétele B2B-hez](active-directory-b2b-support.md)
#### [Gyakori kérdések](active-directory-b2b-faq.md)
### [Helyszíni identitások integrálása az Azure AD Connecttel](./connect/active-directory-aadconnect.md)


## Erőforrások hozzáférésének delegálása
### [Rendszergazdai szerepkörök](active-directory-assign-admin-roles.md)
#### [Rendszergazdai szerepkörök hozzárendelése](active-directory-users-assign-role-azure-portal.md)
### [Felügyeleti egységek](active-directory-administrative-units-management.md)
### [Erőforrás-hozzáférés az Azure-ban](active-directory-understanding-resource-access.md)
### [Szerepköralapú hozzáférés-vezérlés](role-based-access-control-what-is.md)
#### Hozzáférés-hozzárendelések kezelése
##### [Felhasználó szerint](role-based-access-control-manage-assignments.md)
##### [Erőforrás szerint](role-based-access-control-configure.md)
#### [Beépített szerepkörök](role-based-access-built-in-roles.md)
#### [Egyéni szerepkörök](role-based-access-control-custom-roles.md)
#### [Jelentéskészítés](role-based-access-control-access-change-history-report.md)
#### Szerepkörök kezelésének további módjai
##### [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
##### [PowerShell](role-based-access-control-manage-access-powershell.md)
##### [REST](role-based-access-control-manage-access-rest.md)
#### [Hibaelhárítás](role-based-access-control-troubleshooting.md)
### [A jogkivonatok élettartamának beállítása](active-directory-configurable-token-lifetimes.md)

## Identitások védelme
### [Feltételes hozzáférés](active-directory-conditional-access.md)
#### [Első lépések](active-directory-conditional-access-azuread-connected-apps.md)
#### [Támogatott alkalmazások](active-directory-conditional-access-supported-apps.md)
#### [Az eszközszabályzatokkal kapcsolatos tudnivalók](active-directory-conditional-access-device-policies.md)
#### [Hozzáférés beállítása a csatlakoztatott alkalmazásokhoz](active-directory-conditional-access-policy-connected-applications.md)
#### [Gyakori kérdések](active-directory-conditional-faqs.md)
#### [Hibaelhárítás](active-directory-conditional-access-device-remediation.md)
#### [Referencia](active-directory-conditional-access-technical-reference.md)
### Windows Hello
#### [Jelszóhasználat nélküli hitelesítés](active-directory-azureadjoin-passport.md)
#### [A Vállalati Windows Hello engedélyezése](active-directory-azureadjoin-passport-deployment.md)
### Tanúsítványalapú hitelesítés
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Bekapcsolás](active-directory-identityprotection-enable.md)
#### [Biztonsági rések keresése](active-directory-identityprotection-vulnerabilities.md)
#### [Kockázati események](active-directory-identity-protection-risk-events.md)
#### [Értesítések](active-directory-identityprotection-notifications.md)
#### [Bejelentkezési felület](active-directory-identityprotection-flows.md)
#### [Kockázati események szimulálása](active-directory-identityprotection-playbook.md)
#### [Felhasználók tiltásának feloldása](active-directory-identityprotection-unblock-howto.md)
#### [Szószedet](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [AD DS üzembe helyezése Azure-beli virtuális gépeken](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory Azure-beli virtuális gépeken](active-directory-deploying-ws-ad-guidelines.md)
### [Replika tartományvezérlő egy Azure-beli virtuális gépen](active-directory-install-replica-active-directory-domain-controller.md)
### [Új erdő egy Azure-beli virtuális hálózaton](active-directory-new-forest-virtual-machine.md)



## [Az AD FS üzembe helyezése az Azure-ban](active-directory-aadconnect-azure-adfs.md)
### [Magas rendelkezésre állás](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Az aláírás-kivonatoló algoritmus módosítása](active-directory-federation-sha256-guidance.md)

## [Hibaelhárítás](active-directory-troubleshooting.md)


# Referencia
## [PowerShell-parancsmagok](/powershell/ )
## [Java API-referencia](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Szolgáltatási korlátozások](active-directory-service-limits-restrictions.md)

# Kapcsolódó
## [Többtényezős hitelesítés](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD fejlesztőknek](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Erőforrások
## [Díjszabás](https://azure.microsoft.com/pricing/details/active-directory/)
## [MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=active-directory)
## [Azure visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory)
