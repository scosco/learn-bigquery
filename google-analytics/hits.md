# Working with Hit Scope

`hits` contains one of the biggest structs.

Field Name | Data Type | Description
--- | --- | ---
hits | RECORD | This row and nested fields are populated for any and all types of hits.
hits.dataSource | STRING | The data source of a hit. By default, hits sent from analytics.js are reported as "web" and hits sent from the mobile SDKs are reported as "app".
hits.exceptionInfo.exceptions | INTEGER | The number of exceptions sent to Google Analytics.
hits.exceptionInfo.fatalExceptions | INTEGER | The number of exceptions sent to Google Analytics where isFatal is set to true.
hits.hitNumber | INTEGER | The sequenced hit number. For the first hit of each session, this is set to 1.
hits.hour | INTEGER | The hour in which the hit occurred (0 to 23).
~~hits.isSecure~~ | BOOLEAN | This field is deprecated.
hits.isEntrance | BOOLEAN | If this hit was the first pageview or screenview hit of a session, this is set to true.
hits.isExit | BOOLEAN | If this hit was the last pageview or screenview hit of a session, this is set to true.
hits.isInteraction | BOOLEAN | If this hit was an interaction, this is set to true. If this was a non-interaction hit (i.e., an event with interaction set to false), this is false.
hits.latencyTracking | RECORD | This section contains information about events in the Navigation Timing API.
hits.minute | INTEGER | The minute in which the hit occurred (0 to 59).
hits.publisher.* | INTEGER | These fields contains Google Ad Manager backfill information.
hits.time | INTEGER | The number of milliseconds after the visitStartTime when this hit was registered. The first hit has a hits.time of 0
hits.referer | STRING | The referring page, if the session has a goal completion or transaction. If this page is from the same domain, this is blank.
hits.type | STRING | The type of hit. One of: 'pageview', 'screenview', 'event', 'transaction', 'item', 'social', 'exception', 'timing'.  Timing hits are considered an event type in the Analytics backend. When you query time-related fields (e.g., hits.latencyTracking.pageLoadTime), choose hits.type as Event .
~~hits.item~~ | RECORD | This section will be populated for each hit with type = "ITEM". It's only relevant for old ecommerce tracking. Do not use if you're using enhanced ecommerce!

## Refund Info

Field Name | Data Type | Description
--- | --- | ---
hits.refund | RECORD | This row and nested fields are populated for each hit that contains Enhanced Ecommerce REFUND information.
hits.refund.localRefundAmount | INTEGER | Refund amount in local currency, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).
hits.refund.refundAmount | INTEGER | Refund amount, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).

## Social Info

Field Name | Data Type | Description
--- | --- | ---
hits.social | RECORD | This section is populated for each hit with type = "SOCIAL".
hits.social.hasSocialSourceReferral | STRING | A string, either Yes or No, that indicates whether sessions to the property are from a social source.
hits.social.socialInteractionAction | STRING | The social action passed with the social tracking code (Share, Tweet, etc.).
hits.social.socialInteractionNetwork | STRING | The the network passed with the social tracking code (Google+, Twitter, etc.).
hits.social.socialInteractionNetworkAction | STRING | For social interactions, this represents the social network being tracked.
hits.social.socialInteractions | INTEGER | The total number of social interactions.
hits.social.socialInteractionTarget | STRING | For social interactions, this is the URL (or resource) which receives the social network action.
hits.social.socialNetwork | STRING | The social network name. This is related to the referring social network for traffic sources; e.g., Google+, Blogger.
hits.social.uniqueSocialInteractions | INTEGER | The number of sessions during which the specified social action(s) occurred at least once. This is based on the the unique combination of socialInteractionNetwork, socialInteractionAction, and socialInteractionTarget.

 
## Source Property Info
Only exists for exported Roll-up Properties! 

Field Name | Data Type | Description
--- | --- | ---
hits.sourcePropertyInfo | RECORD | This section contains information about source property for rollup properties
hits.sourcePropertyInfo. sourcePropertyDisplayName | STRING | Source property display name of Roll-Up Properties. This is valid for only Roll-Up Properties.
hits.sourcePropertyInfo. sourcePropertyTrackingId | STRING | Source property tracking ID of roll-up properties. This is valid for only roll-up properties.

## Page Info

Field Name | Data Type | Description
--- | --- | ---
hits.page | RECORD | This section is populated for each hit with type = "PAGE".
hits.page.pagePath | STRING | The URL path of the page.
hits.page.pagePathLevel1 | STRING | This dimension rolls up all the page paths in the 1st hierarchical level in pagePath.
hits.page.pagePathLevel2 | STRING | This dimension rolls up all the page paths in the 2nd hierarchical level in pagePath.
hits.page.pagePathLevel3 | STRING | This dimension rolls up all the page paths in the 3d hierarchical level in pagePath.
hits.page.pagePathLevel4 | STRING | This dimension rolls up all the page paths into hierarchical levels. Up to 4 pagePath levels may be specified. All additional levels in the pagePath hierarchy are also rolled up in this dimension.
hits.page.hostname | STRING | The hostname of the URL.
hits.page.pageTitle | STRING | The page title.
hits.page.searchKeyword | STRING | If this was a search results page, this is the keyword entered.
hits.page.searchCategory | STRING | If this was a search-results page, this is the category selected.
hits.contentGroup | RECORD | This section contains information about content grouping. Learn more
hits.contentGroup.contentGroupX | STRING | The content group on a property. A content group is a collection of content that provides a logical structure that can be determined by tracking-code or page-title/URL regex match, or predefined rules. (Index X can range from 1 to 5.)
hits.contentGroup.previousContentGroupX | STRING | Content group that was visited before another content group. (Index X can range from 1 to 5.)
hits.contentGroup.contentGroupUniqueViewsX | STRING | The number of unique content group views. Content group views in different sessions are counted as unique content group views. Both the pagePath and pageTitle are used to determine content group view uniqueness. (Index X can range from 1 to 5.)

## App Info

Field Name | Data Type | Description
--- | --- | ---
hits.contentInfo | RECORD | This section will be populated for each hit with type = "APPVIEW".
hits.contentInfo.contentDescription | STRING | The description of the content being viewed as passed to the SDK.
hits.appInfo | RECORD | This section will be populated for each hit with type = "APPVIEW" or "EXCEPTION".
hits.appInfo.appInstallerId | STRING | ID of the installer (e.g., Google Play Store) from which the app was downloaded.
hits.appInfo.appName | STRING | The name of the application.
hits.appInfo.appVersion | STRING | The version of the application.
hits.appInfo.appId | STRING | The ID of the application.
hits.appInfo.screenName | STRING | The name of the string.
hits.appInfo.landingScreenName | STRING | The landing screen of the session.
hits.appInfo.exitScreenName | STRING | The exit screen of the session.
hits.appInfo.screenDepth | STRING | The number of screenviews per session reported as a string. Can be useful for historgrams.

## Exception Info

Field Name | Data Type | Description
--- | --- | ---
hits.exceptionInfo | RECORD | This section is populated for each hit with type = "EXCEPTION".
hits.exceptionInfo.description | STRING | The exception description.
hits.exceptionInfo.isFatal | BOOLEAN | If the exception was fatal, this is set to true.

## Event Info

Field Name | Data Type | Description
--- | --- | ---
hits.eventInfo | RECORD | This section is populated for each hit with type = "EVENT".
hits.eventInfo.eventCategory | STRING | The event category.
hits.eventInfo.eventAction | STRING | The event action.
hits.eventInfo.eventLabel | STRING | The event label.
hits.eventInfo.eventValue | INTEGER | The event value.

## Custom Info

Field Name | Data Type | Description
--- | --- | ---
~~hits.customVariables~~ | RECORD | Custom variables are an old concept - use custom dimensions/metrics instead!
hits.customDimensions | RECORD | This section contains any hit-level custom dimensions. This is a repeated field and has an entry for each dimension that is set.
hits.customDimensions.index | INTEGER | The index of the custom dimension.
hits.customDimensions.value | STRING | The value of the custom dimension.
hits.customMetrics | RECORD | This section contains any hit-level custom metrics. This is a repeated field and has an entry for each metric that is set.
hits.customMetrics.index | INTEGER | The index of the custom metric.
hits.customMetrics.value | INTEGER | The value of the custom metric.
