[![Scholarship Google](https://img.shields.io/badge/scholarship-Google-brightgreen.svg)](https://www.google.com)
[![Online Classes udacity](https://img.shields.io/badge/online%20classes-Udacity-ff69b4.svg)](https://www.udacity.com)
[![License Apache 2.0](https://img.shields.io/badge/license-Apache%202.0-green.svg)](https://github.com/fjoglar/android-dev-challenge/blob/master/LICENSE.txt)
[![Platform Android](https://img.shields.io/badge/platform-Android-blue.svg)](https://www.android.com)
[![Language Java](https://img.shields.io/badge/language-Java-orange.svg)](https://www.java.com)
# Content Providers
With a Content Provider the application can read data or write data to certain other applications like user's contacts, documents , calendar etc, with only a few lines of code.

Usually data sources like SQLite database are private to the app which created them, this is done for security reasons.

Here is where content providers come in, different apps are reading and writing to the same data source and its job is to provide easily access to the underlying data source.

## General Steps for Using a Content Provider
* Get permission to use the ContentProvider.
* Get the ContentResolver
* Pick one of four basic actions on the data: query, insert, update, delete
* Identify the data you are reading or manipulating to create a URI
* In the case of reading from the ContentProvider, display the information in the UI

### 1. Get permission to use the ContentProvider
Open up the Android manifest, and add  before 
```xml  
<application .../>
```
following permission
```xml
<uses-permission android:name="com.example.udacity.droidtermsexample.TERMS_READ"/>
```
### 2. Get the ContentResolver
```java
ContentResolver resolver = getContentResolver();
```
A ContentResolver manages what content providers are talking to what apps, and keeping all the data in sync, to not turn into a huge traffic jam. The content resolver acts as an intermediary between each app and the content provider, or providers, it wants to access. It handles inter-process communication and keeps everything in sync and running smoothly. Even if you have five processes accessing two content providers.

So wherever we want to use a Content Provider, we'll need to do it through a ContentResolver.

### 3. Pick one of four basic actions on the data: query, insert, update, delete
```java
resolver.query or resolver.insert or resolver.update or resolver.delete
```
![Four Basic Operations Of A Content Provider](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/Four%20Basic%20Operations%20Of%20A%20Content%20Provider.png)

### 4. Identify the data you are reading or manipulating to create a URI
```java
Cursor cursor = resolver.query(DroidTermsExampleContract.CONTENT_URI, null, null, null, null);
```
To specify what data we're actually interested in reading or manipulating, we need to learn about an additional object called a URI.
URI stands for Uniform Resource Identifier, URLs are a subset of URIs that are specifically met to identify network locations such as websites and files on the Web.
A URI is used to specifically identify or give the location of some data.
examine what data has an URI
```java
Uri myUri = DroidTermsExampleContract.CONTENT_URI;
Log.d("GV", myUri.toString());
// outputs => content://com.example.udacity.droidtermsexample/terms
```
This location is how we know exactly what type of data you're querying for.
* content: => Content Provider Prefix
* com.example.udacity.droidtermsexample => Content Authority
* terms => Specific Data

So within the URI is the following information.

```It says, I'm a URI for content providers on Android, and I'm accessing this specific droidternexample provider and I'm only interested in the list of terns stored in droidternexample data.```

For more information about each Content Provider search for his own contract information, like:

[https://udacity.github.io/DroidTermsExampleProvider-Documentation/](https://udacity.github.io/DroidTermsExampleProvider-Documentation/)

### 5. In the case of reading from the ContentProvider, display the information in the UI
 Last but not least

 ## Code Examples
 ### 
 ### The MainActivity where the Content Provider is used (manifest, java class)
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.udacity.example.quizexample">

    <uses-permission android:name="com.example.udacity.droidtermsexample.TERMS_READ"/>

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```
 ```java
package com.udacity.example.quizexample;

import android.content.ContentResolver;
import android.database.Cursor;
import android.os.AsyncTask;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;

import android.content.ContentResolver;

// The Content Provider
import com.udacity.example.droidtermsprovider.DroidTermsExampleContract;

public class MainActivity extends AppCompatActivity {

    // The data from the DroidTermsExample content provider
    private Cursor mData;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //Run the database operation to get the cursor off of the main thread
        new WordFetchTask().execute();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        // Remember to close your cursor!
        mData.close();
    }
    
    // Use an async task to do the data fetch off of the main thread.
    public class WordFetchTask extends AsyncTask<Void, Void, Cursor> {

        // Invoked on a background thread - Make the query to get the data
        @Override
        protected Cursor doInBackground(Void... params) {
            // Get the content resolver
            ContentResolver resolver = getContentResolver();

            // Call the query method on the resolver with the correct Uri from the contract class
            Cursor cursor = resolver.query(DroidTermsExampleContract.CONTENT_URI,
                    null, null, null, null);
            return cursor;
        }

        // Invoked on UI thread
        @Override
        protected void onPostExecute(Cursor cursor) {
            super.onPostExecute(cursor);

            // Set the data for MainActivity
            mData = cursor;

            // last but not least, display the information in the UI
            // the code goes here
            // ...
        }
    } // AsyncTask WordFetchTask

} // MainActivity 
 ```
 ### The Content Provider DroidTermsExampleContract (manifest, java class)
 ```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.udacity.example.droidtermsprovider">

    <application
        android:allowBackup="true"
        android:label="@string/app_name"
        android:supportsRtl="true">

    </application>

</manifest>
```
 ```java
 package com.udacity.example.droidtermsprovider;

import android.content.ContentResolver;
import android.content.ContentUris;
import android.net.Uri;
import android.provider.BaseColumns;

/**
 * The contract between the DroidTermsExample provider and other applications. Contains definitions
 * for the supported URIs and columns.
 */
public class DroidTermsExampleContract implements BaseColumns {

    /**
     * This it the content authority for DroidTermsExample provider.
     */
    public static final String CONTENT_AUTHORITY = "com.example.udacity.droidtermsexample";

    /**
     * This is the {@link Uri} on which all other DroidTermsExample Uris are built.
     */
    public static final Uri BASE_CONTENT_URI = Uri.parse("content://" + CONTENT_AUTHORITY);

    /**
     * The path for terms
     */
    public static final String PATH_TERMS = "terms";

    /**
     * This is the {@link Uri} used to get a full list of terms and definitions.
     */
    public static final Uri CONTENT_URI =
            BASE_CONTENT_URI.buildUpon().appendPath(PATH_TERMS).build();


    /**
     * This is a String type that denotes a Uri references a list or directory.
     */
    public static final String CONTENT_TYPE =
            ContentResolver.CURSOR_DIR_BASE_TYPE + "/" + CONTENT_AUTHORITY + "/" + PATH_TERMS;

    /**
     * This is a String type that denotes a Uri references a single item.
     */
    public static final String CONTENT_ITEM_TYPE =
            ContentResolver.CURSOR_ITEM_BASE_TYPE + "/" + CONTENT_AUTHORITY + "/" + PATH_TERMS;


    // Declaring all these as constants makes code a lot more readable.
    // It also looks a more like SQL.

    /**
     * This is the version of the database for {@link android.database.sqlite.SQLiteOpenHelper}.
     */
    public static final int DATABASE_VERSION = 1;

    /**
     * This is the name of the SQL table for terms.
     */
    public static final String TERMS_TABLE = "term_entries";
    /**
     * This is the name of the SQL database for terms.
     */
    public static final String DATABASE_NAME = "terms";

    /**
     * This is the column name in the SQLiteDatabase for the word.
     */
    public static final String COLUMN_WORD = "word";
    /**
     * This is the column name in the SQLiteDatabase for the definition.
     */
    public static final String COLUMN_DEFINITION = "definition";

    /**
     * This is an array containing all the column headers in the terms table.
     */
    public static final String[] COLUMNS =
            {_ID, COLUMN_WORD, COLUMN_DEFINITION};

    /**
     * This is the index of the ID in the terms table
     */
    public static final int COLUMN_INDEX_ID = 0;
    /**
     * This is the index of the word in the terms table
     */
    public static final int COLUMN_INDEX_WORD = 1;
    /**
     * This is the index of the definition in the terms table
     */
    public static final int COLUMN_INDEX_DEFINITION = 2;

    /**
     * This method creates a {@link Uri} for a single term, referenced by id.
     * @param id The id of the term.
     * @return The Uri with the appended id.
     */
    public static Uri buildTermUriWithId(long id) {
        return ContentUris.withAppendedId(CONTENT_URI, id);
    }
}
 ```
 ## Resources for this article
- Lesson 9 - Content Providers

## License
```
With respect to the rightholders UDACITY, GOOGLE 
```
```
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
