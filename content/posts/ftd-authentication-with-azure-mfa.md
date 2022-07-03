---
tags: ['azure', 'cisco', 'ftd', 'networking', 'security']
author: Gary Ossewaarde
title: FTD Authentication with Azure MFA
categories: ['networking']
date: 2021-07-21T21:45:28-04:00
---

I recently configured Azure MFA to authenticate AnyConnect users connecting to a FTD firewall. This required some odd workarounds.

**Problems to work around**

1. FTD cannot do SAML, must use RADIUS for AnyConnect AAA (note: later versoins of FTD are SAML capable)
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

<iframe frameborder="0" style="width:100%;height:373px;" src="https://viewer.diagrams.net/?tags=%7B%7D&highlight=000000&edit=_blank&layers=1&nav=1&title=Untitled%20Diagram.drawio#R7VrZcuI4FP0aHtvl3fDIksx0TXom1czS80QJWxhNjOWWBYF8fV%2FZEl5k6GTCku5KUgXo6EqW77mbZPec8Wr7C0PZ8hONcNKzzWjbcyY927Z8y4UvgexKJAjsEogZiaRQBUzJE5agKdE1iXDeEOSUJpxkTTCkaYpD3sAQY%2FSxKbagSfOqGYqxBkxDlOjoPyTiyxLte2aF%2F4pJvFRXtkzZs0JKWAL5EkX0sQY5Nz1nzCjl5a%2FVdowToTyll3Lc7YHe%2FcIYTvlzBsS7T4OnO5O4D9PP%2BcPXu78nyW8f7KCcZoOStbzjz8PJx7%2BmgH3GX9c453L1fKdUgiPQkGxSxpc0pilKbip0xOg6jbC4rgmtSuaO0gxAC8D%2FMOc7STdacwrQkq8S2bugKZedfWiWSxDXPXjvEsrpmoX4yA0rG0IsxvyInLdnCEwb0xXmbAfjGE4QJ5vmOpC0sXgvJ4cOGUO7mkBGScrz2sz3AgAB6S6ushXpLJbXovRl8vCjXIFq1W6lggozeYHJOPYxk8kzmub4GjYDpsB2X8R4YxB4Cvi3AHxroIDJVl6ibO3qrXvMCKgHMwle3wyt19phzRZeTrWlMa0Ru8GMEwiXd2iOk3uaE05oCl1zyjldgdLyB8zDpVQoyrMySC%2FIVnBd50%2FNNExILGbggvdRzhl9wGOaUEFKSlNhK0iKhKB3QdaIrnlCUhBTSUBcDAJuJla52sYiNxkh4YxsDYYiss5nOWabYuyCJMkfGYJeYQsifiumxYrw9jjXOjVygD0YNH1T%2BepjlUYCz%2FBKcFnLIYNz0emYGn0%2FdCx3LxTLX6V092f0oYgwkKFsV7nRCVzGd1vpzLYM29O8xu%2FwmX5fedLJCVQraDDoJ1yadoNK%2F%2Buaqo4PeWH0QxDoZ9tCQ6obfsXie4qhbI1QoYPhGu5PzjtnSkIhsPLyYgq%2Bqh9b%2FcKqEONDUWILK0pQnpNQwbckUcNO6O%2FeM%2F19cE1%2FtwcHzSUiG0XgPRQaBe97emudHfLCOkBf4NtFbDhmJ42JfqZw%2F1z63WvS7%2Bg105uojq9Py4HYfxlaumL4D5%2BF25XsCVKw6wdvrWrtiqct6o6RIffcIOeNet6kS%2BcQioUfNDKcXeETsoph5QmZwyd6WjMs7ohEIh7DngE22QKbDSezCV0hks6mQAgJcW7km%2FhUtFgNWpwOWvpBFy1980y0WHqgK%2FQgSpmJxhDcOm%2FywjDUR2heCBQOBSErl8rvIqntVCsSRUVo1AkWO7qWn3U6XzOotuubU9Dm7stSSZxr6cTtjwzrvLnn4k25zxnKWf3k8M0Ur3hL%2BJfa7%2FJYyJOt6kxINHa1xpETIevUR0KXSqT%2F62jSvsZRo3XytF2viGpRomc7Q8tzhqYWKqBnUfwdzOtapGoGpBHoaSxMDT5FeLLHAjMsu0AMu90hgSbYhQVdYD%2FonNbqXIMSDtodBWB1rKGNqbuoZigCcKEBzG42uFSE1VnD5CGd5WiBjZCuMkEc%2FhgK6kbzuPzRJRtB2ohTY56g9GEWYZFny2Ty3SELwnBGHzGboSxLCErDds4%2FUdS3tGOMfcFUT9cdQf98ydrXHOn2T%2Fg0b0Epj0hM8J6w6%2BWupK7fxdxe6CL52tKf0I2FbQP0cXrzzluZnfp%2BEYPq1F270IJ7u1jysvvi%2F5D6u%2FNWV5DWjOFI1M5xuGawAZoRiLy5QfIzhVPHcZvh1Ak0Zm23a%2B%2FjuGc7FLb0Xenv96L0nZab8Xe3LPc%2F7nPc8qLxVF2svm8Ni7rZNifqscY7f0Wvb36fPO%2BS5DmOTl7rZPwKe8vqxYPGawf7vle8crCkjDwBhKqLRfozFwBrT1wu%2F2Bm%2F2bW236dwdGfxY5h65GA%2BgH9dDu8hvW0CH8mec8wg5Pz%2B9ojB%2F31Jb9ZbTvewBgM3MH%2BrzlhaV1yjpYpnOCwwdYPNF9er333KYBeYXVF9lZ5Z5qjm1u7oxwrTqONdV6%2ByHKOrWzQTuCO7RumvinyOyswPzD6g8NmccCboVm9FlnSW71c6tx8Aw%3D%3D"></iframe>


**Identity Services Engine (ISE) Configuration**

- External RADIUS Servers – define your NPS server(s) and shared secret here
- RADIUS Server Sequence 
    - Create a sequence per tunnel group that needs to be differentiated
    - In the Advanced Attribute Settings tab, add the NAS Identifier attribute



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