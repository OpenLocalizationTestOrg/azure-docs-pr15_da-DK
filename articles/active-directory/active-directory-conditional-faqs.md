<properties
    pageTitle="Azure Active Directory betinget Access ofte stillede spørgsmål om | Microsoft Azure"
    description="Ofte stillede spørgsmål om betinget adgang "
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-faq"></a>Azure Active Directory betinget Access ofte stillede spørgsmål

## <a name="which-applications-work-with-conditional-access-policies"></a>Hvilke programmer sammen med betinget access politikker?

**A:** Se emnet [betinget access-hvad programmer understøttes](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Betinget access politikker, håndhæves B2B samarbejde og gæstebrugere?

**A:** Politikker er tvungne for B2B samarbejde brugere. Men i nogle tilfælde kan en bruger kan ikke kunne opfylder kravet om politik, hvis for eksempel en organisation ikke understøtter Multi-Factor godkendelse. 

Politikken er aktuelt ikke gælder for SharePoint gæstebrugere. Gæst relationen vedligeholdes i SharePoint. Gæstebrugere konti ikke er underlagt access politikker på af godkendelsesserveren. Gæsteadgang kan administreres på SharePoint.

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>En SharePoint Online-politik også gælder for OneDrive for Business?

**A:** Ja.
 
## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Hvorfor kan jeg angive en politik på klient-apps som Word eller Outlook?

**A:** Et betinget access-politik angiver krav til at få adgang til en tjeneste og håndhæves, når godkendelse sker i den pågældende tjeneste. Politikken er ikke angivet direkte på et klientprogrammet; i stedet, anvendes den, når den ringer til en tjeneste. For eksempel en politik, der er angivet i SharePoint gælder for klienter, der kalder SharePoint og en politik indstillet på Exchange gælder for Outlook.


## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Gælder en betinget access-politik for tjenestekonti?

**A:** Betinget access politikker gælder for alle brugerkonti. Dette omfatter brugerkonti, der bruges som tjenestekonti. I mange tilfælde er en tjenestekonto, der kører automatisk ikke mulighed for at tilfredsstille en politik. Dette gælder, for eksempel, når MFA er påkrævet. I disse tilfælde kan services konti skal udelukkes fra en politik, ved hjælp af betinget access politik for administration af indstillinger. Lær mere om at anvende en politik til brugere her.
