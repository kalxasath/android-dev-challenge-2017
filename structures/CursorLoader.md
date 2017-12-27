[![Scholarship Google](https://img.shields.io/badge/scholarship-Google-brightgreen.svg)](https://www.google.com)
[![Online Classes udacity](https://img.shields.io/badge/online%20classes-Udacity-ff69b4.svg)](https://www.udacity.com)
[![License Apache 2.0](https://img.shields.io/badge/license-Apache%202.0-green.svg)](https://github.com/fjoglar/android-dev-challenge/blob/master/LICENSE.txt)
[![Platform Android](https://img.shields.io/badge/platform-Android-blue.svg)](https://www.android.com)
[![Language Java](https://img.shields.io/badge/language-Java-orange.svg)](https://www.java.com)
# CursorLoader
A CursorLoader is a subclass of AsyncTaskLoader that queries a ContentProvider, via a ContentResolver and specific URI, and returns a Cursor of desired data. This loader runs its query on a background thread so that it doesn’t block the UI. When a CursorLoader is active, it is tied to a URI, and you can choose to have it monitor this URI for any changes in data; this means that the CursorLoader can deliver new results whenever the contents of our weather database change, and we can automatically update the UI to reflect any weather change!

## Transfort AsyncTaskLoader to CursorLoader
Modify the ForecastAdapter

Our first step will be to modify the ForecastAdapter so that it creates views based on the data in a Cursor (and not an array of Strings). So, in the ForecastAdapter class, we’ll replace the global variable **private String[] mWeatherData** with a Cursor that holds that data: **private Cursor mCursor**.

Then, we’ll change the onBindViewHolder method so that it takes all of the data from a cursor and uses that to populate the views. To read the correct weather data from a Cursor, you have to read that data from the correct position with a call to: **mCursor.moveToPosition(position)**. Then we’ll extract all the relevant values from the Cursor, and finally set the text to display the correct weather summary. To read in data from a Cursor, you can use methods like getInt or getString on that Cursor, ex. **double highInCelsius = mCursor.getDouble(MainActivity.INDEX_WEATHER_MAX_TEMP)**. Then create a string to summarize the weather **String weatherSummary = dateString + " - " + description + " - " + highAndLowTemperature** and display it!

You'll also want to update the getItemCount method to reflect the number of items in the Cursor, which you can get with a call to: mCursor.getCount().

And replace the setWeatherData method with a new method called swapCursor, which should take in a new Cursor and update the value of the old Cursor. This is how changes in data will be managed.

Here is the swapCursor method:
```java
void swapCursor(Cursor newCursor) {
        mCursor = newCursor;
        // After the new Cursor is set, call notifyDataSetChanged
        notifyDataSetChanged();
}
```

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
> Written with [StackEdit](https://stackedit.io/).
