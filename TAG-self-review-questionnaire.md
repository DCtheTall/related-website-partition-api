# [TAG Self-Review Questionnaire: Security and Privacy](https://w3ctag.github.io/security-questionnaire/)

## 2. Questions to Consider

### 2.1. What information does this feature expose, and for what purposes?

This feature does not introduce any new information to the first party site.
First party sites must allow embedded origins to use this API usng a Permissions Policy, so the first party will know which third party embeds invoke the Related Website Partition (RWP) API on its site.

Third parties can use this feature to detect if they are embedded in a Related Website Set by invoking the API and observing the result of the returned Promise.
This information is normally available in the public [RWS JSON file](https://github.com/GoogleChrome/related-website-sets/blob/main/related_website_sets.JSON) and sites in publicly visible RWS host their `/.well-known/related-website-set.json`.
There are some cases, such as when browsers are managed by an enterprise policy that uses a [custom list](https://chromeenterprise.google/policies/#RelatedWebsiteSetsOverrides), that this API would expose RWS membership to third parties embedded on those sites.
Third parties would only have access to this information if the top-level site allows them via a Permissions Policy.

The primary purpose of the RWP API is to give third parties access to a storage partition that is shared across sites under the privacy boundary of the RWS.
This storage partition does not contain any data that the third party does not put there themselves.
This storage partition would be available when RWS is enabled even if the browser has third-party cookie restrictions enabled.

### 2.2. Do features in your specification expose the minimum amount of information necessary to implement the intended functionality?

Yes.
All of the information the RWP API exposes to first and third parties are necessary for the feature's intended functionality and do not reveal any more.
The RWP API provides a general-purpose storage partition which we cannot restrict.
This storage partition is only available under a single RWS, which limits the exposure of the information stored in the partition.

### 2.3. Do the features in your specification expose personal information, personally-identifiable information (PII), or information derived from either?

This feature does not reveal any personal information about the user.
Third parties using this API may wish to store PII, this storage partition will only be available to that origin when the top-level site is in the current RWS.

### 2.4. How do the features in your specification deal with sensitive information?

See 2.3.

### 2.5. Does data exposed by your specification carry related but distinct information that may not be obvious to users?

No, it does not.

### 2.6. Do the features in your specification introduce state that persists across browsing sessions?

Yes, this feature gives third parties embedded in a site in an RWS access to a storage partition that is available across different browsing sessions.
This storage partition should be cleared when a user wishes to clear storage for the storage origin.
User agents should also clear this storage if the user is able to delete data for all sites in the RWS.

### 2.7. Do the features in your specification expose information about the underlying platform to origins?

No, this API does not expose any information about the underlying platform.

### 2.8. Does this specification allow an origin to send data to the underlying platform?

No, this API does not send information to the underlying platform.

### 2.9. Do features in this specification enable access to device sensors?

No, this API does not provide access to device sensors.

### 2.10. Do features in this specification enable new script execution/loading mechanisms?

No, this API does not provide any new capabilities to load or execute scripts.

### 2.11. Do features in this specification allow an origin to access other devices?

No, this API does not provide access to any other devices.

### 2.12. Do features in this specification allow an origin some measure of control over a user agent’s native UI?

No, this API does not provide control over a user agent's native UI.
The user agent may choose to indicate in its native UI when this feature is being used.

### 2.13. What temporary identifiers do the features in this specification create or expose to the web?

This API does not create or expose any temporary identifiers to the web.
Third parties using this API may use the storage partition to persist identifiers provided by other APIs, but this could be done with any storage mechanism.

### 2.14. How does this specification distinguish between behavior in first-party and third-party contexts?

This API is only available to third-party contexts that are embedded in a Related Website Set.

### 2.15. How do the features in this specification work in the context of a browser’s Private Browsing or Incognito mode?

Related Website Sets is not available in Private Browsing or Incognito mode, so the RWP API is not available as well.

### 2.16. Does this specification have both "Security Considerations" and "Privacy Considerations" sections?

[Yes.](https://github.com/explainers-by-googlers/related-website-partition-api#privacy-and-security-considerations)

### 2.17. Do features in your specification enable origins to downgrade default security protections?

No, this feature does not provide any capability to opt out of any security protections.

### 2.18. What happens when a document that uses your feature is kept alive in BFCache (instead of getting destroyed) after navigation, and potentially gets reused on future navigations back to the document?

This feature gives third parties access to a persistent storage partition for use within an RWS.
The storage partition will be available to the document after navigation, but the document will have to invoke the RWP API again to access it.

### 2.19. What happens when a document that uses your feature gets disconnected?

The document may continue to use the storage partition in the JavaScript code being executed by the user agent.

### 2.20. Does your spec define when and how new kinds of errors should be raised?

Yes, this API returns a Promise which will reject with relevant errors if the current context should not be able to use the RWP API.

### 2.21. Does your feature allow sites to learn about the user’s use of assistive technology?

No, this feature does not reveal any information to sites about a user's use of assistive technology.

### 2.22. What should this questionnaire have asked?

None that I can think of! 😀

## 3. Review of Threat Models

### 3.1. Passive Network Attackers

This API uses a storage partition which is only available to JavaScript code running on the page.
Passive network attackers cannot observe any content of this storage partition that the site does not explicitly choose to send over the network on its own.

### 3.2. Active Network Attackers

See 3.1.

### 3.3. Same-Origin Policy Violations

Like most other forms of JavaScript storage data, the data a third party stores using RWP is only available to that particular third-party origin.

### 3.4. Third-Party Tracking

The RWP API does allow third parties to track user activity across sites in the same RWS.
The API does not give third parties access to any activity outside of the given RWS.
Third parties would still need to invoke the Storage Access API in order to gain access to any storage outside the RWS.

### 3.5. Legitimate Misuse

While the RWP API does not in of itself present an opportunity for misuse, a malicious third party could use the storage partition provided by the RWP API to store identifiers or other data which it acquires through illegitimate or invasine means.
