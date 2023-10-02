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
   8. [Budget Management and Billing](#budget-management-and-billing)
   9. [Monetization](#monetization)

## Introduction

The Fediverse, a decentralized network of social media platforms, offers an alternative to traditional, centralized platforms. However, its growth and sustainability face significant challenges due to a lack of viable monetization options. 

Currently, the Fediverse relies heavily on volunteers for server maintenance and moderation, and on donations for financial resources. This model, while embodying the community spirit of the Fediverse, is not scalable. As the network expands, the demand for resources also increases, straining the current model. Furthermore, content creators lack a reliable way to monetize their work, limiting the diversity and quality of content.

This document proposes a solution to these challenges: a decentralized advertising framework built on the ActivityPub protocol. This framework aims to provide relevant, user-controlled advertising that respects privacy, offers robust monetization, and integrates seamlessly with the existing ActivityPub ecosystem.

## Goals

1. **Sustainability**: Provide a viable revenue stream for server operators and content creators, ensuring the long-term sustainability of the Fediverse.
  
2. **User Autonomy**: Give users control over their ad experience, with options to fine-tune or opt-out.

3. **Privacy**: Design the framework with privacy as a cornerstone, aligning with the values of the Fediverse.

4. **Seamless Integration**: Leverage existing ActivityPub protocols for easy adoption and implementation.

5. **Transparency**: Maintain open standards and clear revenue sharing models for all stakeholders.

## ActivityPub Integration

This section discusses how the framework integrates with ActivityPub.

### Ads as Activities

Advertisements are treated as specialized ActivityPub activities, encoded in JSON-LD. This approach aims to unify the advertising model with the existing ActivityPub standards, making it easier for developers to implement the framework.

Key Advantages:

- Native Distribution: Ads can be seamlessly federated across ActivityPub-compliant servers, reaching a wider audience without additional protocols.
- User Interaction: The inherent interactivity of activities enables users to engage with ads through actions like 'Like,' 'Share,' or custom interactions defined for the advertising framework.
- Structured Metadata: Ads can carry additional metadata such as target demographics, monetization pointers, and more, all within the ActivityPub JSON-LD context.

Data Model: Ads are defined as a custom ActivityPub type called Ad. Here is an example data model for an ad:

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
  "content": "Discover our new eco-friendly smartwatch.",
  "targetDemographics": ["18-35", "Tech enthusiasts"],
  "budget": 1000,
  "monetization": {
    "platform": "platform_payment_pointer",
    "creator": "creator_payment_pointer"
  }
}
```

#### Fields Explained

- `@context`: The context defines the interpretation of the activity. Includes both the standard ActivityPub context and a custom context for the ad.
- `id`: A unique identifier for the ad, used for tracking.
- `type`: Specifies the activity as an ad, indicated by `ad:Ad`.
- `actor`: URI of the advertiser's account for accountability.
- `content`: A string containing the ad message.
- `targetDemographics`: An array of tags to target specific demographics.
- `budget`: The total budget for the ad campaign.
- `monetization`: 
  - `platform`: Payment pointer for the platform.
  - `creator`: Payment pointer for the content creator. Used for revenue sharing.


#### Server-Side Handling
**Validation:** When an ad activity is received, the server should validate the JSON-LD against the predefined schema for ad:Ad. Any non-compliant activities should be rejected.

**Routing:** The server will route the ad activity based on the targetDemographics field, matching it against the AdPreferences of users on the instance.

#### Client-Side Handling
**Display:** Clients will recognize the ad:Ad type and can render it in a designated ad space within the UI.

**Interaction:** Users can interact with the ad through standard ActivityPub actions like Like, Dislike, or custom-defined ad interaction verbs.

#### Advantages
**Standardization:** By using ActivityPub, we ensure that ads can be federated across any ActivityPub-compliant server without needing a new protocol or standard.

**Extensibility:** The JSON-LD format is extensible, allowing for future additions to the ad format without breaking compatibility.

**User Control:** Representing ads as activities allows for user interaction and feedback, which can be used to refine ad targeting and performance metrics.

### User Preferences

This section discusses how user preferences are handled, including initial setup, customization, and dynamic updates.

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

##### Fields Explained

- `@context`: The context defines the interpretation of the activity. It includes the standard ActivityPub context.
- `type`: The type of the activity. In this case, it's `AdPreferences`, indicating that this activity is for storing user's ad preferences.
- `actor`: The URI of the user's account. It allows for accountability and tracking of who created the preferences.
- `timestamp`: An ISO 8601 timestamp that represents when the `AdPreferences` activity was last updated.
- `preferences`: 
  - `frequency`: The preferred frequency of ads, represented as a string like "Low", "Medium", "High", or "None".
  - `format`: An array of preferred ad formats, such as "Image", "Text", "Video", etc.
  - `categories`: An array of preferred ad categories, such as "Tech", "Fashion", "Sports", etc.
- `interests`: An array of interest tags used to match ads to the user's interests.
- `keywords`: An array of keywords that represent the user's interests.
- `exclusions`: An array of exclusion tags used to exclude certain categories of ads from being displayed to the user.

#### Server-Side Handling

**Preference Matching:** When an ad is received, the server uses these preferences to decide which users are eligible to see the ad.

**Updating Preferences:** Servers should listen for updates to this AdPreferences activity to refresh user preferences in real-time.

#### Client-Side Handling
**UI for Preferences:** Clients should provide an intuitive UI where users can set and update their ad preferences, divided into the sections explained above.

**Local Storage:** These preferences can be stored locally on the client and synced with the server.

#### Initial Setup and Customization

1. **Initial Prompt**: During first login or account creation, offer users an option to customize ad preferences. Make this step skippable to avoid intrusiveness.
  
2. **Categories & Keywords**: Provide a list of standardized categories and keywords for users to select from. These could be sourced from the server or agreed upon across the Fediverse.

#### Dynamic Updates and User Experience

1. **Dynamic Prompts**: Utilize non-intrusive, contextual prompts to encourage users to update their preferences based on their activities, such as liking posts related to "technology."

2. **Privacy Measures**: Store preferences locally or encrypt them before sending to the server. Be transparent about data usage.

3. **UI Integration**: Seamlessly integrate the ad preferences UI into the existing client settings or profile customization sections.

### Server-Side Logic

Server-side logic handles ad validation, user matching, ad display, metrics collection, and revenue distribution. This section outlines the core responsibilities and implementation details for server-side logic.

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

### Client Extensions

Modifications to ActivityPub clients are essential for a seamless user experience in this ad framework. This section outlines the key features and technical details for implementing client-side support for decentralized advertising.

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

By making advertisers ActivityPub actors, they can utilize the existing ActivityPub infrastructure for creating, distributing, and tracking ads. This section outlines how advertisers can create, target, and track ads within the ActivityPub framework.

#### Ad Creation and Targeting
**Ad Creation Methods:** Advertisers have the flexibility to create ads using various methods. They can utilize a client that supports ad creation or employ a standalone tool that sends ActivityPub activities. Given the decentralized nature of the system, anyone can develop a tool for this purpose.

**Ad Targeting:** Advertisers can specify targeting criteria that match against user AdPreferences.

**Publishing & Distribution:** Once an ad is created, it's published as an ActivityPub activity and federated to appropriate servers based on the targeting criteria.

### Metrics & Reporting

Effective advertising is data-driven. This section details how metrics are collected, what metrics are important, and how they are presented to advertisers for insights and optimization.

#### Key Metrics

1. **Impressions**: The number of times an ad is viewed.
  
2. **Clicks**: The number of times an ad is clicked.

3. **Interactions**: Other engagements like 'Likes,' 'Shares,' or custom interactions designed for the ad system.

4. **Opt-Outs**: The number of users who opted out of viewing the ad.

5. **Comments:** Captured when a user performs a 'Create' activity with a 'Note' object in response to the ad.

#### Implementation

1. **Metrics Collection**: Servers collect these metrics as users interact with ads. Each interaction carries the 'actor' and 'object' fields, representing the user and the ad respectively. To ensure privacy, the 'actor' field is anonymized or omitted during metric collection.
  
2. **Metrics API**: An API endpoint is exposed for advertisers to fetch these metrics. Alternatively, metrics can be sent back as specialized ActivityPub activities.

3. **Dashboard**: A real-time dashboard can be integrated into the advertiser's client for easy access to metrics.

4. **Data Aggregation**: Metrics are aggregated at the server level to anonymize data and comply with privacy regulations.

### Opt-Out & Consent

This section describes the mechanisms for user opt-out and how consent is managed.

#### Core Features

1. **Opt-Out Button**: A simple "Opt-Out of Ads" button should be present in the ad preferences UI, allowing users to disable ad activities for their account.
  
2. **Category Exclusion**: Users should be able to exclude certain categories of ads from being displayed.

3. **Data Use Disclosure**: A clear, concise explanation should be present in the ad preferences UI about what data is used for targeting.

4. **Consent on First Use**: The first time an ad is displayed, users are presented with a small, unobtrusive consent form to agree to ad viewing.

#### Implementation

1. **UI Elements**: Integrate opt-out and disclosure elements into the ad preferences UI in the client application.
  
2. **Server Logic**: The server should respect opt-out settings and category exclusions when distributing ads.

3. **ActivityPub Flags**: Use ActivityPub activities or flags in user profiles to indicate their opt-out or consent status.

By implementing these features, the framework ensures users have complete control over their data and ad experience.

### Budget Management and Billing

This section explains how advertisers set budgets for their ad campaigns, how these budgets are distributed and tracked across the network, and how billing is handled.

#### Budget Setup and Ad Creation

1. **Initial Budget and Ad Creation**: The advertiser selects a client that supports ad creation. They define their ad and set an initial budget, which is the total amount they are willing to spend on the ad campaign.

2. **Payment to Client**: The initial budget is paid to the origin server or specialized advertising client through a secure payment gateway. This payment is used to fund the distribution and display of the ad across the network.

#### Ad Distribution, Budget Allocation, and Impression Management

1. **Federation of Ad Activity**: After the payment is confirmed, the client creates an `ad:Ad` activity that includes the ad content and the budget. This activity is then distributed to all participating servers in the network.

2. **Initial Allocation**: The origin server allocates initial blocks of impressions to participating servers via a `Create` + `AdCampaign` activity.

3. **Budget Distribution**: The initial budget is divided among the participating servers based on a predetermined allocation strategy. This could be based on factors such as the number of users, engagement levels, or other criteria defined by the advertiser.

4. **Local Tracking**: Each server that receives the `ad:Ad` activity maintains a local tally of the impressions consumed. This allows for real-time tracking and ensures that the budget is not exceeded.

5. **Top-Up Requests**: Participating servers request additional impressions via a `Request` activity when nearing their allocated limit. This request includes an impressions report providing interim ad reporting back to the origin server and advertiser.

6. **Top-Up Allocation**: The origin server grants additional impressions via an `Update` + `AdCampaign` activity.

7. **Real-Time Tracking**: The origin server keeps a real-time tally of impressions allocated and used.

8. **Interim Settlement**: Optionally, at predefined intervals or milestones, the origin server settles any financial transactions, such as revenue sharing with participating servers.

9. **Ad Termination**: Once the total budget is exhausted, the ad campaign is terminated. Any remaining funds are returned to the advertiser.

10. **Final Reconciliation**: At the end of the campaign, final ad:BudgetSummary activity is sent to the "Budget Topic" for reconciliation, and any unused budget is returned to the advertiser as credit. 

11. **Payment Pointers**: Payments are made to the payment pointers provided by each participating server, ensuring secure and transparent transactions. Each participating server provides a payment pointer during the initial ad campaign setup.

12. **Confirmation**: A PaymentConfirmation ActivityPub activity is sent to confirm the successful transfer of funds.

### Monetization

This section outlines how revenue is generated, shared, and managed within the ActivityPub-based ad framework.

#### Revenue Model

1. **Pay-Per-Thousand Impressions (CPM)**: The primary revenue model is based on Cost Per Thousand Impressions (CPM). Advertisers pay for blocks of 1,000 impressions, which provides a predictable and scalable revenue stream. This model aligns with industry standards and is easily understood by advertisers.

2. **Revenue Goals**: The goal of the revenue model is to create a sustainable and equitable distribution of income. This includes covering operational costs for server operators, providing income for content creators, and potentially rewarding users for their engagement.

#### Revenue Distribution

1. **Platform Share**: A significant portion of the ad revenue is allocated to the hosting platform. This revenue helps cover the costs of server operation, maintenance, and development, ensuring the sustainability of the platform.

2. **Creator Share**: Content creators are also beneficiaries of the revenue model. A share of the revenue is distributed based on user interaction with ads on their content. This incentivizes high-quality content creation and fosters a vibrant and active community.

3. **User Share**: In addition to platform and creator shares, users can optionally receive a small share of revenue for interacting with ads. This model not only incentivizes user engagement but also respects and rewards users' attention.

4. **Transparency and Fairness**: The revenue distribution model is designed to be transparent and fair. Detailed reports on ad impressions and revenue distribution are made available to all stakeholders. This transparency builds trust and encourages participation in the ad network.

5. **Adoption and Integration**: The proposed monetization model is designed to be easily adopted and integrated into existing ActivityPub platforms. It respects user privacy, provides value to advertisers, and ensures a fair distribution of revenue, making it an attractive option for the Fediverse.

## Conclusion

This document outlines a robust strategy for implementing a decentralized advertising framework in the Fediverse using ActivityPub. The proposed framework addresses the core challenge of revenue generation, which has been a significant hurdle for the sustainability of the Fediverse. By introducing a revenue model that respects user privacy and provides value to advertisers, we can ensure the longevity of the Fediverse.




