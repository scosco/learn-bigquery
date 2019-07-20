# Working with Session Scope

Looking at the schema of the Google Analytics data, "session scope" means everything on root level, excluding arrays. 
We'll be making an exception for the array `customDimensions` since the typical use case here is to retrieve only one element per field!

This compiles a list of the following simple fields:

Field Name | Data Type | Description
---- | ---- | ----
clientId | STRING | Unhashed version of the Client ID for a given user associated with any given visit/session.
fullVisitorId | STRING | The unique visitor ID (also known as client ID).
~~visitorId~~ | NULL | This field is **deprecated**. Use *fullVisitorId* instead.
userId | STRING | Overridden User ID sent to Analytics.
visitNumber | INTEGER | The session number for this user. If this is the first session, then this is set to 1.
visitId | INTEGER | An identifier for this session. This is part of the value usually stored as the _utmb cookie. This is only unique to the user. For a completely unique ID, you should use a combination of fullVisitorId and visitId.
visitStartTime | INTEGER | The timestamp (expressed as POSIX time).
date | STRING | The date of the session in YYYYMMDD format.
socialEngagementType | STRING | Engagement type, either "Socially Engaged" or "Not Socially Engaged".
channelGrouping | STRING | The Default Channel Group associated with an end user's session for this View.

------

With some fields being structs (not arrays!):

## Totals
The struct `totals` contains aggregations of the whole session and fields for conveniently querying data.

Field Name | Data Type | Description
---- | ---- | ----
totals | RECORD | This section contains aggregate values across the session.
totals.bounces | INTEGER | Total bounces (for convenience). For a bounced session, the value is 1, otherwise it is null.
totals.hits | INTEGER | Total number of hits within the session.
totals.newVisits | INTEGER | Total number of new users in session (for convenience). If this is the first visit, this value is 1, otherwise it is null.
totals.pageviews | INTEGER | Total number of pageviews within the session.
totals.screenviews | INTEGER | Total number of screenviews within the session.
totals.sessionQualityDim | INTEGER | An estimate of how close a particular session was to transacting, ranging from 1 to 100, calculated for each session. A value closer to 1 indicates a low session quality, or far from transacting, while a value closer to 100 indicates a high session quality, or very close to transacting. A value of 0 indicates that Session Quality is not calculated for the selected time range.
totals.timeOnScreen | INTEGER | The total time on screen in seconds.
totals.timeOnSite | INTEGER | Total time of the session expressed in seconds.
totals.totalTransactionRevenue | INTEGER | Total transaction revenue, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).
~~totals.transactionRevenue~~ | INTEGER | This field is **deprecated**. Use "totals.totalTransactionRevenue" instead (see above).
totals.transactions | INTEGER | Total number of ecommerce transactions within the session.
totals.UniqueScreenViews | INTEGER | Total number of unique screenviews within the session.
totals.visits | INTEGER | The number of sessions (for convenience). This value is 1 for sessions with interaction events. The value is null if there are no interaction events in the session.

`totals` offers a nice challenge for mastering the array `hits`: Try to repeat the results from `totals` using sub-queries (no flattening!) on top of `hits`! this can get quite challenging, so don't feel bad if you can't find a solution for all of them just now. Here is an example:

```sql
SELECT 
  SUM( totals.hits ) AS hits,
  SUM( (select count(*) from unnest(hits)) ) AS sq_hits,
  SUM( totals.pageviews ) AS pageviews,
  SUM( (select count(*) from unnest(hits) where type='PAGE') ) AS sq_pageviews
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801` 
```

Revisit this section after you know more about fields in `hits`!

## Traffic Source

Field Name | Data Type | Description
---- | ---- | ----
trafficSource | RECORD | This section contains information about the Traffic Source from which the session originated.
trafficSource.adContent | STRING | The ad content of the traffic source. Can be set by the utm_content URL parameter.
trafficSource.adwordsClickInfo | RECORD | This section contains information about the Google Ads click info if there is any associated with this session. Analytics uses the last non-direct click model.
trafficSource. adwordsClickInfo.adGroupId | INTEGER | The Google ad-group ID.
trafficSource. adwordsClickInfo.adNetworkType | STRING | Network Type. Takes one of the following values: {“Google Search", "Content", "Search partners", "Ad Exchange", "Yahoo Japan Search", "Yahoo Japan AFS", “unknown”}
trafficSource. adwordsClickInfo.campaignId | INTEGER | The Google Ads campaign ID.
trafficSource. adwordsClickInfo.creativeId | INTEGER | The Google ad ID.
trafficSource. adwordsClickInfo.criteriaId | INTEGER | The ID for the targeting criterion.
trafficSource. adwordsClickInfo.criteriaParameters | STRING | Descriptive string for the targeting criterion.
trafficSource. adwordsClickInfo.customerId | INTEGER | The Google Ads Customer ID.
trafficSource. adwordsClickInfo.gclId | STRING | The Google Click ID.
trafficSource. adwordsClickInfo.isVideoAd | BOOLEAN | True if it is a Trueview video ad.
trafficSource. adwordsClickInfo.page | INTEGER | Page number in search results where the ad was shown.
trafficSource. adwordsClickInfo.slot | STRING | Position of the Ad. Takes one of the following values:{“RHS", "Top"}
trafficSource. adwordsClickInfo.targetingCriteria | RECORD | Google Ads targeting criteria for a click. There are multiple types of targeting criteria, but should have only one value for each criterion.
trafficSource. adwordsClickInfo.targetingCriteria. boomUserlistId | INTEGER | Remarketing list ID (if any) in Google Ads, derived from matching_criteria in click record.
trafficSource.campaign | STRING | The campaign value. Usually set by the utm_campaign URL parameter.
trafficSource.campaignCode | STRING | Value of the utm_id campaign tracking parameter, used for manual campaign tracking.
trafficSource.isTrueDirect | BOOLEAN | True if the source of the session was Direct (meaning the user typed the name of your website URL into the browser or came to your site via a bookmark), This field will also be true if 2 successive but distinct sessions have exactly the same campaign details. Otherwise NULL.
trafficSource.keyword | STRING | The keyword of the traffic source, usually set when the trafficSource.medium is "organic" or "cpc". Can be set by the utm_term URL parameter.
trafficSource.medium | STRING | The medium of the traffic source. Could be "organic", "cpc", "referral", or the value of the utm_medium URL parameter.
trafficSource.referralPath | STRING | If trafficSource.medium is "referral", then this is set to the path of the referrer. (The host name of the referrer is in trafficSource.source.)
trafficSource.source | STRING | The source of the traffic source. Could be the name of the search engine, the referring hostname, or a value of the utm_source URL parameter.

## Device 

Field Name | Data Type | Description
---- | ---- | ----
device | RECORD | This section contains information about the user devices.
device.browser | STRING | The browser used (e.g., "Chrome" or "Firefox").
device.browserSize | STRING | The viewport size of users' browsers. This captures the initial dimensions of the viewport in pixels and is formatted as width x height, for example, 1920x960.
device.browserVersion | STRING | The version of the browser used.
device.deviceCategory | STRING | The type of device (Mobile, Tablet, Desktop).
device.mobileDeviceInfo | STRING | The branding, model, and marketing name used to identify the mobile device.
device.mobileDeviceMarketingName | STRING | The marketing name used for the mobile device.
device.mobileDeviceModel | STRING | The mobile device model.
device.mobileInputSelector | STRING | Selector (e.g., touchscreen, joystick, clickwheel, stylus) used on the mobile device.
device.operatingSystem | STRING | The operating system of the device (e.g., "Macintosh" or "Windows").
device.operatingSystemVersion | STRING | The version of the operating system.
device.mobileDeviceBranding | STRING | The brand or manufacturer of the device.
device.flashVersion | STRING | The version of the Adobe Flash plugin that is installed on the browser.
device.javaEnabled | BOOLEAN | Whether or not Java is enabled in the browser.
device.language | STRING | The language the device is set to use. Expressed as the IETF language code.
device.screenColors | STRING | Number of colors supported by the display, expressed as the bit-depth (e.g., "8-bit", "24-bit", etc.).
device.screenResolution | STRING | The resolution of the device's screen, expressed in pixel width x height (e.g., "800x600").

## Geo Network

Field Name | Data Type | Description
---- | ---- | ----
geoNetwork | RECORD | This section contains information about the geography of the user.
geoNetwork.continent | STRING | The continent from which sessions originated, based on IP address.
geoNetwork.subContinent | STRING | The sub-continent from which sessions originated, based on IP address of the visitor.
geoNetwork.country | STRING | The country from which sessions originated, based on IP address.
geoNetwork.region | STRING | The region from which sessions originate, derived from IP addresses. In the U.S., a region is a state, such as New York.
geoNetwork.metro | STRING | The Designated Market Area (DMA) from which sessions originate.
geoNetwork.city | STRING | Users' city, derived from their IP addresses or Geographical IDs.
geoNetwork.cityId | STRING | Users' city ID, derived from their IP addresses or Geographical IDs. The city IDs are the same as the Criteria IDs found at https://developers.google.com/analytics/devguides/collection/protocol/v1/geoid.
geoNetwork.latitude | STRING | The approximate latitude of users' city, derived from their IP addresses or Geographical IDs. Locations north of the equator have positive latitudes and locations south of the equator have negative latitudes.
geoNetwork.longitude | STRING | The approximate longitude of users' city, derived from their IP addresses or Geographical IDs. Locations east of the prime meridian have positive longitudes and locations west of the prime meridian have negative longitudes.
geoNetwork.networkDomain | STRING | The domain name of user's ISP, derived from the domain name registered to the ISP's IP address.
geoNetwork.networkLocation | STRING | The names of the service providers used to reach the property. For example, if most users of the website come via the major cable internet service providers, its value will be these service providers' names.

## Custom Dimensions

As mentioned, this field is an array. As you will see, it is an array of structs that needs a certain way of approaching it.

Field Name | Data Type | Description
---- | ---- | ----
customDimensions | RECORD | This section contains any user-level or session-level custom dimensions that are set for a session. This is a repeated field and has an entry for each dimension that is set.
customDimensions.index | INTEGER | The index of the custom dimension.
customDimensions.value | STRING | The value of the custom dimension.

Index and value are both specified when setting up Google Analytics and tracking. To get a specific value you need to know its respective index. You could think - since they sit in an array - the custom dimensions might be ordered by the array index and you could simply use `SAFE_OFFSET()` to retrieve an element, but this **is wrong**. Google Analytics doesn't guarantee any order in this array, so you have to use the field *index*. It just means a little query to get this information, though, e.g.

```sql
SELECT 
  date,
  (SELECT value FROM UNNEST(customDimensions) WHERE index=4) cd4
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801` 
LIMIT 1000
```

Of course, you can also use sub-queries in the `WHERE` clause and group values after retrieving them:

```sql
SELECT 
  date,
  (SELECT value FROM UNNEST(customDimensions) WHERE index=4) cd4,
  SUM(totals.visits) as sessions
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801` 
WHERE 
  -- accept this session only if we count more than 0 cases where index is 4 and value is not null
  (SELECT COUNT(*) > 0 FROM UNNEST(customDimensions) WHERE index=4 and value is not null)
GROUP BY 1, 2
LIMIT 1000
```
