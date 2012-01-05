Content Provider for WIMM News Reader
=============

Android allows applications to share data across applications using [content providers][providers]. In our [News Reader application][androidsx-wimm] for [WIMM][wimm] we provide a read-only content provider so that you can access the feeds and items into your application.

This repository includes a class that will help you access the content providers with constants with its URI, columns, etc.

For what?
-------
One interesting use case is to create a WIMM watchface that shows the latest news from the different feeds every X minutes.

Instructions
-------

### Import the content provider definitions

Copy `News.java` into your project. You can change the package or keep it, as you wish.

### Usage

All content providers implement a common interface for querying the provider and returning results.

It's an interface that clients use indirectly, most generally through `ContentResolver` objects. You get a ContentResolver by calling `getContentResolver()` from within the implementation of an Activity or other application component:

```java
    ContentResolver cr = getContentResolver();
```
You can then use the ContentResolver's methods to interact with News Reader provider, such as `query` method ([official documentation how to query][official]).

#### Code samples

Get active feeds (the ones that the user has selected, and have items):

```java
Cursor cursor = null;
try {
    final String[] projection = new String[] {News.Feeds._ID, News.Feeds.TITLE,  News.Feeds.FEED_URL, 
        News.Feeds.LAST_UPDATE };
    cursor = getContentResolver().query(News.Feeds.CONTENT_URI, projection, News.Feeds.ACTIVE + " = 1",
        null, News.Feeds.TITLE + " ASC");

    if (cursor != null && cursor.moveToFirst()) {
        do {
            int id = cursor.getInt(cursor.getColumnIndex(News.Feeds._ID));
            String title = cursor.getString(cursor.getColumnIndex(News.Feeds.TITLE));

            // do something
        } while (cursor.moveToNext());
    }
} finally {
    if (cursor != null) {
        cursor.close();
    }
}
```        

Get items/stories from a specific feed (`feedId`):       

```java        
Cursor cursor = null;
try {
    // items from feed could also be retrieved querying News.Items filtering the feed id
    final Uri aFeedUri = ContentUris.withAppendedId(News.Feeds.CONTENT_URI, feedId);
    final Uri allStoriesUriInFeedUri = Uri.withAppendedPath(aFeedUri, News.TABLE_ITEMS);
    final String[] projection = new String[] { News.Items._ID, News.Items.TITLE, News.Items.CONTENT,
            News.Items.ITEM_URL, News.Items.THUMBNAIL_URL, News.Items.DATE };
                                
    cursor = getContentResolver().query(allStoriesUriInFeedUri, projection, null, null,
            News.Items.POSITION + " DESC");
    
    if (cursor != null && cursor.moveToFirst()) {
        do {
            
            String title = cursor.getString(cursor.getColumnIndex(News.Items.TITLE));
            Date date = new Date(cursor.getLong(cursor.getColumnIndex(News.Items.DATE)));
            
            // do something
        } while (cursor.moveToNext());
    }
    return result;
} finally {
    if (cursor != null) {
        cursor.close();
    }
}
```

### URIs (not needed to use the provider)

Each content provider exposes a public URI (wrapped as a Uri object) that uniquely identifies its data set. All URIs for providers begin with the string "content://".

We provide contants in the file News.java where you don't need to know the URIs, but just in case it would be useful for someone the URIs we provide are:

    content://com.androidsx.microrss.provider.NewsProvider/categories
    content://com.androidsx.microrss.provider.NewsProvider/categories/#
    content://com.androidsx.microrss.provider.NewsProvider/feeds
    content://com.androidsx.microrss.provider.NewsProvider/feeds/#
    content://com.androidsx.microrss.provider.NewsProvider/feeds/#/items
    content://com.androidsx.microrss.provider.NewsProvider/items
    content://com.androidsx.microrss.provider.NewsProvider/items/#

The URI constant is used in all interactions with the content provider. Every ContentResolver method takes the URI as its first argument. It's what identifies which provider the ContentResolver should talk to and which table of the provider is being targeted.

About
-------
WIMM News Reader is an application for the wearable android device WIMM, developed by [androidsx][androidsx]

![application](http://www.androidsx.com/wp-content/uploads/2012/01/wimm.png)

[official]: http://developer.android.com/guide/topics/providers/content-providers.html#querying
[wimm]: http://www.wimm.com
[androidsx]: http://www.androidsx.com
[providers]: http://developer.android.com/guide/topics/providers/content-providers.html
[androidsx-wimm]: http://www.androidsx.com/news-reader-for-wimm/
