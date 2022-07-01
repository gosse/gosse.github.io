---
tags: ['azure', 'cisco', 'ftd', 'networking', 'security']
type: "post"
author: Gary Ossewaarde
title: FTD Authentication with Azure MFA
categories: ['networking']

---

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">I recently configured Azure MFA to authenticate AnyConnect users connecting to a FTD firewall. This required some odd workarounds.

**Problems to work around**

1. FTD cannot do SAML, must use RADIUS for AnyConnect AAA
2. Microsoft NPS with Azure MFA extension must be used for RADIUS Integration to Azure MFA
3. Microsoft NPS has a limited number of attributes it can filter incoming RADIUS requests on
4. Customer has a need to only allow certain AD groups access to certain tunnel groups

**Authentication Flow**

1. Firewall sends Access-Request to ISE
2. ISE adds RADIUS:NAS Identifier attribute to Access-Request based on CVPN3000/ASA/PIX7x-Tunnel-Group-Name
3. NPS filters incoming requests based on NAS Identifier to appropriately authenticate different tunnel groups and does primary authentication against AD.
4. NPS requests secondary authentication from Azure MFA
5. Azure MFA completes MFA with user, based on the user’s default MFA method. There is no way to select this during the authentication sequence.
6. NPS sends result back to ISE
7. ISE proxies result back to FTD

**Authentication Diagram**

![Ftd anyconnect azuremfa](https://www.next-hop-self.com/wp-content/uploads/2021/07/ftd-anyconnect-azuremfa.png "ftd-anyconnect-azuremfa.png")

**Identity Services Engine (ISE) Configuration**

- External RADIUS Servers – define your NPS server(s) and shared secret here
- RADIUS Server Sequence 
    - Create a sequence per tunnel group that needs to be differentiated
    - In the Advanced Attribute Settings tab, add the NAS Identifier attribute

![Ise modify attrib](https://www.next-hop-self.com/wp-content/uploads/2021/07/ise-modify-attrib.png "ise-modify-attrib.png")

- Policy Sets 
    - Create one policy set per tunnel group
    - Conditions: NAS-Port-Type Virtual AND CVPN3000/ASA/PIX7x-Tunnel-Group-Name = MyGroupName
    - Server Sequence: proxy sequence created above

**Network Policy Server (NPS) Configuration**

- Install Azure MFA extension and link to Azure AD – <https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-nps-extension>\] 
    - Protip: use a new NPS server. Once you install the extension, all requests authenticated locally will be subject to MFA
- RADIUS Clients 
    - In the NPS console, configure your ISE servers as RADIUS clients, using the same shared secret as above.
    - Vendor – RADIUS Standard
- Network Policies 
    - Create one per tunnel group name
    - Type of network access server – Unspecified
    - Conditions 
        - NAS Identifier – the identifier ISE added
        - User Groups – group you want to filter on
    - NAS-Port-Type Virtual
    - Authentication Methods – I have them all enabled, including PEAP and EAP-MSCHAPv2 You minimally need PAP, but I have lots of logs that say “Custom” for the Authentication Type.
    - Note: The Microsoft documentation says: *“**PAP** supports all the authentication methods of Azure MFA in the cloud: phone call, one-way text message, mobile app notification, OATH hardware tokens, and mobile app verification code. \*CHAPV2 and EAP\* support phone call and mobile app notification.”*

**Firepower Threat Defense (FTD) Configuration**

- Configure ISE as your RADIUS servers for AnyConnect AAA.
- Set the timeout to 60 seconds or longer—if users are waiting for an SMS, phone call, push notification, etc., you want to give them enough time to do it.

**User Experience**

- Users will put in their username and password at the AnyConnect client
- If their preferred option (set at <https://account.activedirectory.windowsazure.com/Proofup.aspx>) uses a code (verification code form app or text message), they will see a pop-up after their first authentication.
- If their preferred option is a voice call or push notification, the authentication will wait until the verification is complete before granting access. Make sure all timeouts in the chain are long enough to support this.

**Troubleshooting**

The NPS logs are a pain to use for troubleshooting. I wrote a quick python script to help parse the logs. You can find it here: <https://github.com/gosse/interpret-nps-logs>.

</div></div></div></div>