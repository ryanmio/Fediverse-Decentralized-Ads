# Decentralized Advertising in the Fediverse Using ActivityPub

## Table of Contents
1. [Introduction](#introduction)
2. [Goals](#goals)
3. [ActivityPub Integration](#activitypub-integration)
   1. [Ads as Activities](#ads-as-activities)
   2. [User Preferences](#user-preferences)
   3. [Server-Side Logic](#server-side-logic)
   4. [Client Extensions](#client-extensions)
   5. [Advertiser Interaction](#advertiser-interaction)
   6. [Metrics & Reporting](#metrics--reporting)
   7. [Opt-Out & Consent](#opt-out--consent)
   8. [Monetization](#monetization)
4. [Security & Fraud Prevention](#security--fraud-prevention)
5. [Compliance](#compliance)
6. [Pilot Testing & Feedback](#pilot-testing--feedback)

## Introduction

The Fediverse, a decentralized network of social media platforms, offers an alternative to traditional, centralized platforms. However, its growth and sustainability face significant challenges due to a lack of viable monetization options. Server maintenance and moderation require financial resources, and relying solely on volunteers and donations is not scalable. This document proposes a decentralized advertising system built atop the ActivityPub protocol as a solution to these challenges. The system aims to provide relevant, user-controlled advertising that respects privacy, offers robust monetization, and seamlessly integrates with the existing ActivityPub ecosystem.

## Goals

1. **Sustainability**: Provide a viable revenue stream for server operators and content creators, ensuring the long-term sustainability of the Fediverse.
  
2. **User Autonomy**: Give users control over their ad experience, with options to fine-tune or opt-out.

3. **Privacy**: Design the system with privacy as a cornerstone, aligning with the values of the Fediverse.

4. **Seamless Integration**: Leverage existing ActivityPub protocols for easy adoption and implementation.

5. **Transparency**: Maintain open standards and clear revenue sharing models for all stakeholders.

## ActivityPub Integration

### Ads as Activities

In this architecture, advertisements are treated as specialized ActivityPub activities, encoded in JSON-LD. This approach aims to unify the advertising model with the existing ActivityPub standards, making it easier for developers to implement the system.

Ads in this system are represented as specialized ActivityPub activities, allowing for native distribution and interaction within the Fediverse. By using activities, ads gain several advantages:

1. **Native Distribution**: Ads can be seamlessly federated across ActivityPub-compliant servers, reaching a wider audience without additional protocols.
  
2. **User Interaction**: The inherent interactivity of activities enables users to engage with ads through actions like 'Like,' 'Share,' or custom interactions defined for the advertising system.

3. **Structured Metadata**: Ads can carry additional metadata such as target demographics, monetization pointers, and more, all within the ActivityPub JSON-LD context.

For example, an ad activity could look like this:

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    {
      "ad": "https://example.com/schema/Ad"
    }
  ],
  "id": "unique_ad_id",
  "type": "ad:Ad",
  "actor": "advertiser_account_uri",
  "content": "Discover our new eco-friendly smartwatch. Track your fitness goals while caring for the planet.",
  "targetDemographics": ["18-35", "Tech enthusiasts", "Environmentally conscious"],
  "budget": 1000,
  "monetization": {
    "platform": "platform_payment_pointer",
    "creator": "creator_payment_pointer"
  }
}
```
This structure makes it easier for both servers and clients to handle ads in a standardized way, while providing advertisers with the ability to target their messages effectively.

#### Data Model

Ads are defined as a custom ActivityPub `type` called `Ad`. Here is an example data model for an ad:

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    {
      "ad": "https://example.com/schema/Ad"
    }
  ],
  // The context defines the interpretation of the activity. It includes the standard ActivityPub context and a custom context for the ad.

  "id": "unique_ad_id",
  // A unique identifier for the ad. It should be a unique string that can be used to track and identify the ad.

  "type": "ad:Ad",
  // The type of the activity. In this case, it's `ad:Ad`, indicating that this activity is an ad.

  "actor": "advertiser_account_uri",
  // The URI of the advertiser's account. It allows for accountability and tracking of who created the ad.

  "content": "String containing the ad content",
  // The content of the ad. It should be a string containing the ad message.

  "targetDemographics": ["Array", "of", "demographic", "tags"],
  // An array of demographic tags. These tags are used to target the ad to specific demographics.

  "budget": "Total budget for the ad campaign",
  // The total budget for the ad campaign. It can be used for ad distribution logic.

  "monetization": {
    "platform": "platform_payment_pointer",
    "creator": "creator_payment_pointer"
  }
  // An object that includes payment pointers for both the platform and the content creator. It's used for revenue sharing.
}
```
1. @context: This is a standard field in ActivityPub activities. It defines the context in which the activity is interpreted. In this case, it includes the standard ActivityPub context and a custom context for the ad.
   The custom context "ad": "https://example.com/schema/Ad" is a URL where the schema for the custom ad:Ad type is defined. This allows for extensibility and custom properties for ads.

2. id: This is a unique identifier for the ad. It should be a unique string that can be used to track and identify the ad.

3. type: This is the type of the activity. In this case, it's ad:Ad, indicating that this activity is an ad.

4. actor: This is the URI of the advertiser's account. It allows for accountability and tracking of who created the ad.
   "actor": "advertiser_account_uri",

5. content: This is the content of the ad. It should be a string containing the ad message.

6. targetDemographics: This is an array of demographic tags. These tags are used to target the ad to specific demographics.

7. budget: This is the total budget for the ad campaign. It can be used for ad distribution logic.

8. monetization: This is an object that includes payment pointers for both the platform and the content creator. It's used for revenue sharing.
   "monetization": {
     "platform": "platform_payment_pointer",
     "creator": "creator_payment_pointer"
   }


#### Server-Side Handling
##### Validation: When an ad activity is received, the server should validate the JSON-LD against the predefined schema for ad:Ad. Any non-compliant activities should be rejected.

##### Routing: The server will route the ad activity based on the targetDemographics field, matching it against the AdPreferences of users on the instance.

#### Client-Side Handling
#####Display: Clients will recognize the ad:Ad type and can render it in a designated ad space within the UI.

##### Interaction: Users can interact with the ad through standard ActivityPub actions like Like, Dislike, or custom-defined ad interaction verbs.

#### Advantages
##### Standardization: By using ActivityPub, we ensure that ads can be federated across any ActivityPub-compliant server without needing a new protocol or standard.

##### Extensibility: The JSON-LD format is extensible, allowing for future additions to the ad format without breaking compatibility.

##### User Control: Representing ads as activities allows for user interaction and feedback, which can be used to refine ad targeting and performance metrics.

### User Preferences

User preferences for advertisements are also treated as specialized ActivityPub activities or as custom fields in a user's ActivityPub profile. This ensures that users have granular control over the type of ads they see, while also keeping their data within the ActivityPub ecosystem.

#### Data Model

Here's an example of a user preference modeled as a custom ActivityPub activity:

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "type": "AdPreferences",
  "actor": "https://example.com/users/john_doe",
  "timestamp": "2022-01-01T00:00:00Z",
  "preferences": {
    "frequency": "Low",
    "format": ["Image", "Text"],
    "categories": ["Tech", "Fashion"]
  },
  "interests": ["Open Source", "Sustainability"],
  "keywords": ["Python", "Zero Waste"],
  "exclusions": ["Alcohol", "Gambling"]
}
```

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  // The context defines the interpretation of the activity. It includes the standard ActivityPub context.

  "type": "AdPreferences",
  // The type of the activity. In this case, it's `AdPreferences`, indicating that this activity is for storing user's ad preferences.

  "actor": "user_account_uri",
  // The URI of the user's account. It allows for accountability and tracking of who created the preferences.

  "timestamp": "ISO_8601_timestamp",
  // An ISO 8601 timestamp that represents when the `AdPreferences` activity was last updated.

  "preferences": {
    "frequency": "Preferred ad frequency",
    // The preferred frequency of ads. It should be a string like "Low", "Medium", "High", or "None".

    "format": ["Preferred", "ad", "formats"],
    // An array of preferred ad formats. These could be strings like "Image", "Text", "Video", etc.

    "categories": ["Preferred", "ad", "categories"]
    // An array of preferred ad categories. These could be strings like "Tech", "Fashion", "Sports", etc.
  },

  "interests": ["Array", "of", "interest", "tags"],
  // An array of interest tags. These tags are used to match ads to the user's interests.

  "keywords": ["Array", "of", "keywords"],
  // An array of keywords that represent the user's interests. These keywords can be used to match ads to the user's interests in addition to the `interests` field.

  "exclusions": ["Array", "of", "exclusion", "tags"]
  // An array of exclusion tags. These tags are used to exclude certain categories of ads from being displayed to the user.
}
```

#### Server-Side Handling

##### Preference Matching: When an ad is received, the server uses these preferences to decide which users are eligible to see the ad.

##### Updating Preferences: Servers should listen for updates to this AdPreferences activity to refresh user preferences in real-time.

#### Client-Side Handling
##### UI for Preferences: Clients should provide an intuitive UI where users can set and update their ad preferences, divided into the sections explained above.

##### Local Storage: These preferences can be stored locally on the client and synced with the server.

#### Advantages
##### User Autonomy: Users can fine-tune their ad experience, enabling or disabling categories as they see fit.

##### Privacy: All preferences are stored within the ActivityPub ecosystem, ensuring data isn't shared with external parties.

#####Dynamic Updating: As users change their preferences, these updates are immediately reflected in the ads they see, thanks to real-time syncing between clients and servers.

### Server-Side Logic

The server-side logic is central to the effective functioning of this decentralized advertising system. It handles ad validation, user matching, ad display, metrics collection, and revenue distribution. This section outlines the core responsibilities and implementation details for server-side logic.

#### Core Responsibilities

1. **Ad Receipt & Validation**: Servers should validate incoming ad activities, ensuring they comply with a predefined schema for `ad:Ad`.
  
2. **User Matching**: Upon receiving an ad, the server matches it against users' `AdPreferences` to decide who sees the ad.

3. **Ad Display Logic**: Servers determine where and how the ad appears—timeline, separate ad section, etc.

4. **Metrics Collection**: Servers automatically collect metrics such as impressions, clicks, and other interactions with ad activities.

5. **Revenue Sharing**: Implement logic for automated revenue distribution based on user interactions with ads.

#### Implementation

1. **Data Models**: Extend existing data models to accommodate new ad activities and user preferences.
  
2. **Routing & Federation**: Modify federation logic to handle ad activities—where they get sent, and to whom.

3. **Query Logic**: Implement server-side queries to match ads against user preferences.

4. **Caching**: Cache frequent queries and ad activities to reduce computational load and latency.

5. **Metrics API**: Expose an API endpoint for advertisers to fetch metrics or send them as ActivityPub activities.

6. **Payment Logic**: Implement server-side logic for handling payments and revenue distribution, which could be based on standard payment pointers or integrated payment APIs.

By addressing these responsibilities, servers act as the linchpin that ties advertisers, users, and monetization together in a cohesive, efficient, and privacy-respecting manner.

### Client Extensions

Modifications to ActivityPub clients are essential for a seamless user experience in this ad system. This section outlines the key features and technical details for implementing client-side support for decentralized advertising.

#### Core Features

1. **Ad Preferences UI**: Integrate a settings panel where users can manage their `AdPreferences` activity or profile fields.
  
2. **Ad Display**: Include a designated area in the timeline or a separate tab for displaying ads.

3. **Ad Interactions**: Add buttons or actions for users to interact with ads, like 'Like,' 'Dislike,' 'Hide,' etc.

4. **Opt-Out Option**: Provide a simple, straightforward way for users to opt out of ads entirely.

#### Implementation

1. **UI/UX Design**: Design the user interfaces for ad-related settings and displays, ensuring it aligns with the existing UI elements.
  
2. **API Integration**: Extend the client APIs to handle new ad activities and user preference settings.

3. **Data Storage**: Store `AdPreferences` locally on the client, with an option to sync them with the server for consistency across devices.

4. **Notifications**: Optionally, add notifications for new, relevant ads or updates to the user's share of revenue, if applicable.

5. **Feedback Loop**: Enable users to report problematic ads easily, which can be sent back as specialized ActivityPub activities for review and action.

By implementing these features, ActivityPub clients can provide a user-friendly, intuitive interface for interacting with ads while giving users full control over their ad experience.

### Advertiser Interaction

Advertisers play a crucial role in this ecosystem. This section outlines how advertisers can create, target, and track ads within the ActivityPub framework.

#### Ad Creation and Targeting
##### Ad Creation Methods: Advertisers have the flexibility to create ads using various methods. They can utilize a client that supports ad creation or employ a standalone tool that sends ActivityPub activities. Given the decentralized nature of the system, anyone can develop a tool for this purpose.

##### Ad Targeting: Advertisers can specify targeting criteria that match against user AdPreferences.

##### Publishing & Distribution: Once an ad is created, it's published as an ActivityPub activity and federated to appropriate servers based on the targeting criteria.

#### Metrics and Reporting
##### Metrics Collection: Servers collect metrics like views, clicks, and interactions and send them back to advertisers.

##### Dashboard: Advertisers could have a dashboard to view these metrics in real-time.

##### API for Metrics: A metrics API can be exposed for more advanced analytics and reporting needs.

By making advertisers ActivityPub actors, they can utilize the existing ActivityPub infrastructure for creating, distributing, and tracking ads. This ensures a unified and efficient experience for all stakeholders.

### Metrics & Reporting

Effective advertising is data-driven. This section details how metrics are collected, what metrics are important, and how they are presented to advertisers for insights and optimization.

#### Key Metrics

1. **Impressions**: The number of times an ad is viewed.
  
2. **Clicks**: The number of times an ad is clicked.

3. **Interactions**: Other engagements like 'Likes,' 'Shares,' or custom interactions designed for the ad system.

4. **Opt-Outs**: The number of users who opted out of viewing the ad.

#### Implementation

1. **Metrics Collection**: Servers collect these metrics as users interact with ads. Data is aggregated periodically to maintain user privacy.
  
2. **Metrics API**: An API endpoint is exposed for advertisers to fetch these metrics. Alternatively, metrics can be sent back as specialized ActivityPub activities.

3. **Dashboard**: A real-time dashboard can be integrated into the advertiser's client for easy access to metrics.

4. **Data Aggregation**: Metrics are aggregated at the server level to anonymize data and comply with privacy regulations.

By meticulously collecting and presenting these metrics, advertisers can gain valuable insights into their campaigns, enabling data-driven decisions for better ROI.

### Opt-Out & Consent

User consent and the ability to opt-out are paramount in a privacy-respecting ad ecosystem. This section describes the mechanisms for user opt-out and how consent is managed.

#### Core Features

1. **Opt-Out Button**: A simple "Opt-Out of Ads" button should be present in the ad preferences UI, allowing users to disable ad activities for their account.
  
2. **Category Exclusion**: Users should be able to exclude certain categories of ads from being displayed.

3. **Data Use Disclosure**: A clear, concise explanation should be present in the ad preferences UI about what data is used for targeting.

4. **Consent on First Use**: The first time an ad is displayed, users are presented with a small, unobtrusive consent form to agree to ad viewing.

#### Implementation

1. **UI Elements**: Integrate opt-out and disclosure elements into the ad preferences UI in the client application.
  
2. **Server Logic**: The server should respect opt-out settings and category exclusions when distributing ads.

3. **Legal Texts**: Prepare GDPR and other privacy regulation-compliant texts for data use and consent forms.

4. **ActivityPub Flags**: Use ActivityPub activities or flags in user profiles to indicate their opt-out or consent status.

By implementing these features, the system ensures users have complete control over their data and ad experience while staying compliant with privacy laws.

### Monetization

Monetization is a key motivator for both advertisers and content creators. This section outlines how revenue is generated, shared, and managed within the ActivityPub-based ad system.

#### Revenue Models

1. **Pay-Per-Click (PPC)**: Advertisers pay based on the number of clicks their ads receive.
  
2. **Pay-Per-Impression (PPI)**: Advertisers pay based on the number of times their ad is viewed.

3. **Pay-Per-Interaction (PPIx)**: Advertisers pay based on custom interactions like 'Likes,' 'Shares,' etc.

#### Revenue Distribution

1. **Platform Share**: A certain percentage of ad revenue goes to the hosting platform.
  
2. **Creator Share**: Content creators can also receive a share of the revenue based on user interaction with ads on their content.

3. **User Share**: Optionally, users can receive a small share of revenue for interacting with ads, incentivizing engagement.

#### Implementation

1. **Payment Logic**: Servers implement logic to handle revenue distribution, which could be tied to standard payment pointers or integrated payment APIs.
  
2. **Transparency**: A transparent breakdown of revenue shares should be available to all stakeholders.

3. **Billing and Payouts**: Implement a system for billing advertisers and executing payouts to platforms and creators.

4. **ActivityPub Revenue Activities**: Specialized ActivityPub activities can be used to record and notify of revenue events.

By clearly defining monetization models and revenue distribution mechanisms, the ad system aims to be a profitable venture for all parties involved, while maintaining user trust and engagement.

## Security & Fraud Prevention

Online advertising is susceptible to various types of fraud and malicious activities. This section outlines safeguards.

1. **Rate Limiting**: Implement server-side rate limiting to prevent ad spam.
  
2. **Validation**: Rigorous validation of ad activities to prevent malicious content.

3. **Anomaly Detection**: Real-time monitoring for unusual patterns indicating fraud.

## Compliance

1. **GDPR**: Ensure all data collection and processing is GDPR-compliant.
  
2. **CCPA**: Similar compliance needs for California residents.

3. **Data Minimization**: Collect only the data necessary for the functioning of the ad system.

## Pilot Testing & Feedback

1. **Alpha Test**: Limited initial rollout to a small user and advertiser base.
  
2. **Feedback Mechanism**: Easy ways for testers to provide feedback.

3. **Iterations**: Use feedback for iterative improvements before wider release.


## Conclusion

This document outlines a robust strategy for implementing a decentralized advertising system in the Fediverse using ActivityPub. The proposed system addresses the core challenge of revenue generation, which has been a significant hurdle for the sustainability of the Fediverse. By introducing a revenue model that respects user privacy and provides value to advertisers, we can ensure the longevity of the Fediverse.




