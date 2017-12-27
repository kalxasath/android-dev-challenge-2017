[![Scholarship Google](https://img.shields.io/badge/scholarship-Google-brightgreen.svg)](https://www.google.com)
[![Online Classes udacity](https://img.shields.io/badge/online%20classes-Udacity-ff69b4.svg)](https://www.udacity.com)
[![License Apache 2.0](https://img.shields.io/badge/license-Apache%202.0-green.svg)](https://github.com/fjoglar/android-dev-challenge/blob/master/LICENSE.txt)
[![Platform Android](https://img.shields.io/badge/platform-Android-blue.svg)](https://www.android.com)
[![Language Java](https://img.shields.io/badge/language-Java-orange.svg)](https://www.java.com)
# Add a touch helper to the RecyclerView
This is a typical touch helper to the RecyclerView
```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Set the RecyclerView to its corresponding view
        mRecyclerView = (RecyclerView) findViewById(R.id.recyclerViewTasks); 

        new ItemTouchHelper(new ItemTouchHelper.SimpleCallback(0, ItemTouchHelper.LEFT | ItemTouchHelper.RIGHT) {
            @Override
            public boolean onMove(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder, RecyclerView.ViewHolder target) {
                return false;
            }

            // Called when a user swipes left or right on a ViewHolder
            @Override
            public void onSwiped(RecyclerView.ViewHolder viewHolder, int swipeDir) {
                // Here is where you'll implement swipe to delete

                // (1) Construct the URI for the item to delete
                //[Hint] Use getTag (from the adapter code) to get the id of the swiped item

                // (2) Delete a single row of data using a ContentResolver

                // (3) Restart the loader to re-query for all tasks after a deletion
                
            }
        }).attachToRecyclerView(mRecyclerView);
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
