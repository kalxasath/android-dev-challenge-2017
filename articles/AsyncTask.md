# AsyncTask
AsyncTask is a generic class, meaning that it takes parameterized types in its constructor.

![AsyncTask Generic Class](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/AsyncTask%20Generic%20Class.png)

Each one of these generic parameters is to find as a Java variable argument with the three dots.
Which means that it is technically passed as an array in Java World.
### Three types of arguments
The three types used by an AsyncTask are the following:

> **Params**, parameter type sent to the task upon execution.

> **Progress**, type published to update progress during the background computation.

> **Result**, the type of the result of the background computation.

These three parameters correspond to three primary functions you can override in AsyncTask.

![AsyncTask Primary Functions](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/AsyncTask%20Primary%20Functions.png)

There is also an onPreExecute function to help complete the pattern.

![AsyncTask onPreExecute Function](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/AsyncTask%20onPreExecute%20Function.png)

### Execute AsyncTask
You call **Execute** with the parameters to be set to the background task.
```java
new myAsyncTask.execute(args)
```
### Execution plan

![AsyncTask Execution Plan](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/AsyncTask%20Execution%20Plan.png)

### Execution plan - several steps

![AsyncTask Several Steps](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/AsyncTask%20Several%20Steps.png)


### Implement AsyncTask
The first to-do is to make an AsyncTask inside of the MainActicity.
```java
import android.os.AsyncTask;

public class MainActivity extends AppCompatActivity {
	private TextView mWeatherTextView;
	
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_layout);
		mWeatherTextView = (TextView) findViewById(R.id.tv_weather_data);
        
        runAsyncTask();
    }

	private void runAsyncTask() {
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
		}
		
		@Override
		protected void onPostExecute(String[] weatherData) {
            if (weatherData != null) {
                for (String weatherString : weatherData) {
	                // updates the TextView in the activity_layout
                    mWeatherTextView.append((weatherString) + "\n\n\n");
                }
            }
		}
	}
}
```
### Resources for this article
- Lesson 3 - Connect to the Internet
