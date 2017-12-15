
[![Scholarship Google](https://img.shields.io/badge/scholarship-Google-brightgreen.svg)](https://www.google.com)
[![Online Classes udacity](https://img.shields.io/badge/online%20classes-Udacity-ff69b4.svg)](https://www.udacity.com)
[![License Apache 2.0](https://img.shields.io/badge/license-Apache%202.0-green.svg)](https://github.com/fjoglar/android-dev-challenge/blob/master/LICENSE.txt)
[![Platform Android](https://img.shields.io/badge/platform-Android-blue.svg)](https://www.android.com)
[![Language Java](https://img.shields.io/badge/language-Java-orange.svg)](https://www.java.com)

# AsyncTask
AsyncTask is a generic class, meaning that it takes parameterized types in its constructor.

![AsyncTask Generic Class](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/AsyncTask%20Generic%20Class.png)

Each one of these generic parameters is to find as a Java variable argument with the three dots.
Which means that it is technically passed as an array in Java World.
## Three types of arguments
The three types used by an AsyncTask are the following:

> **Params**, parameter type sent to the task upon execution.

> **Progress**, type published to update progress during the background computation.

> **Result**, the type of the result of the background computation.

These three parameters correspond to three primary functions you can override in AsyncTask.

![AsyncTask Primary Functions](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/AsyncTask%20Primary%20Functions.png)

There is also an onPreExecute function to help complete the pattern.

![AsyncTask onPreExecute Function](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/AsyncTask%20onPreExecute%20Function.png)

## Execute AsyncTask
You call **Execute** with the parameters to be set to the background task.
```java
new myAsyncTask.execute(args)
```
## Execution plan

![AsyncTask Execution Plan](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/AsyncTask%20Execution%20Plan.png)

## Execution plan - several steps

![AsyncTask Several Steps](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/AsyncTask%20Several%20Steps.png)


## Implement AsyncTask
The first to-do is to make an AsyncTask inside of the MainActicity.
```java
import android.os.AsyncTask;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.widget.TextView;

public class MainActivity extends AppCompatActivity {
	private TextView mWeatherTextView;
	
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_layout);
		mWeatherTextView = (TextView) findViewById(R.id.tv_weather_data);
        
        new myAsyncTask.execute("my localtion for weather data")
    }

	public class myAsyncTask extends AsyncTask<String, Void, String[]> {
		@Override
		protected String doInBackground(URL... params) {
            if (params.length == 0) {
                return null;
            }

            String location = params[0];
            URL weatherRequestUrl = NetworkUtils.buildUrl(location);

            try {
                String jsonWeatherResponse = NetworkUtils
                        .getResponseFromHttpUrl(weatherRequestUrl);

                String[] simpleJsonWeatherData = OpenWeatherJsonUtils
                        .getSimpleWeatherStringsFromJson(MainActivity.this, jsonWeatherResponse);

                return simpleJsonWeatherData;

            } catch (Exception e) {
                e.printStackTrace();
                return null;
            }
		} // doInBackground
		
		@Override
		protected void onPostExecute(String[] weatherData) {
            if (weatherData != null) {
                for (String weatherString : weatherData) {
	                // updates the TextView in the activity_layout
                    mWeatherTextView.append((weatherString) + "\n\n\n");
                }
            }
		}
	} // myAsyncTask
} // MainActivity 
```
## Resources for this article
- Lesson 3 - Connect to the Internet

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
