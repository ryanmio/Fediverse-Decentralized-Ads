# ActivityStreams Extensions for Decentralized Advertising in the Fediverse

## Summary

This proposal outlines extensions to the ActivityStreams vocabulary to support a decentralized advertising framework in the Fediverse using ActivityPub. The goal is to create a sustainable revenue model for the Fediverse by treating ads as specialized ActivityPub activities and user preferences as specialized activities or custom fields in a user's ActivityPub profile.

## History

The Fediverse, a decentralized network of social media platforms, has faced significant challenges in terms of growth and sustainability due to a lack of viable monetization options. This proposal aims to address this need by introducing a decentralized advertising framework built on the ActivityPub protocol.

## Requirements

The proposed extensions to the ActivityStreams vocabulary include the following new types of activities, objects, and properties:

- An `ad:Ad` activity type for representing advertisements.
- An `AdPreferences` activity type for representing user preferences for advertisements.
- Additional properties for these new activity types, such as `targetDemographics`, `budget`, and `monetization` for the `ad:Ad` activity, and `frequency`, `format`, `categories`, `interests`, `keywords`, and `exclusions` for the `AdPreferences` activity.

## Example

Here are examples of how these new activity types and properties could be used in JSON-LD format:

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

## About the New Properties

The new properties added to the `ad:Ad` and `AdPreferences` activities represent the following:

- `targetDemographics`: An array of tags to target specific demographics.
- `budget`: The total budget for the ad campaign.
- `monetization`: 
  - `platform`: Payment pointer for the platform.
  - `creator`: Payment pointer for the content creator. Used for revenue sharing.
- `frequency`: The preferred frequency of ads, represented as a string like "Low", "Medium", "High", or "None".
- `format`: An array of preferred ad formats, such as "Image", "Text", "Video", etc.
- `categories`: An array of preferred ad categories, such as "Tech", "Fashion", "Sports", etc.
- `interests`: An array of interest tags used to match ads to the user's interests.
- `keywords`: An array of keywords that represent the user's interests.
- `exclusions`: An array of exclusion tags used to exclude certain categories of ads from being displayed to the user.

## Full Framework
For more details, please refer to the full documentation on [GitHub](https://github.com/ryanmio/Fediverse-Decentralized-Ads/blob/main/docs/Decentralized_Advertising_Fediverse_ActivityPub.md).

## References

- [ActivityPub](https://www.w3.org/TR/activitypub/)
- [ActivityStreams](https://www.w3.org/TR/activitystreams-core/)

## Copyright

The authors of this proposal have waived all copyright and related or neighboring rights to this work, to the extent possible under law.
