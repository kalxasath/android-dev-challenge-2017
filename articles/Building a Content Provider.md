[![Scholarship Google](https://img.shields.io/badge/scholarship-Google-brightgreen.svg)](https://www.google.com)
[![Online Classes udacity](https://img.shields.io/badge/online%20classes-Udacity-ff69b4.svg)](https://www.udacity.com)
[![License Apache 2.0](https://img.shields.io/badge/license-Apache%202.0-green.svg)](https://github.com/fjoglar/android-dev-challenge/blob/master/LICENSE.txt)
[![Platform Android](https://img.shields.io/badge/platform-Android-blue.svg)](https://www.android.com)
[![Language Java](https://img.shields.io/badge/language-Java-orange.svg)](https://www.java.com)
# Building a Content Provider
In this article, we will go through the steps to build a content provider to act as a layer between the database that stores the information an the UI that displays it.

## ToDo List Application Structure

![ToDo List Application Structure](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/ToDo%20List%20Application%20Structure.png)

## Index
- [Main Structure of an Empty Content Provider](#main-structure-of-an-empty-content-provider)
- [Steps to Create a Provider](#steps-to-create-a-provider)
- [Build the Content Provider](#build-the-content-provider)
- [Define The URI Structure](#define-the-uri-structure)
- [URI Structure for the ToDo list](#uri-structure-for-the-todo-list)
- [Build The URIMatcher](#build-the-urimatcher)
- [Resolver To Database Flow](#resolver-to-database-flow)
- [Overview Of Provider Methods](#overview-of-provider-methods)
- [The CRUD methods](#the-crud-methods)
- [Coding the INSERT method](#coding-the-insert-method)
- [Hook It Up To The UI](#hook-it-up-to-the-ui)
- [What Does the CustomCursorAdapter do?](#what-does-the-customcursoradapter-do)
- [Coding the QUERY method](#coding-the-query-method)
- [Hook It Up To The UI - Display the Tasks](#hook-it-up-to-the-ui---display-the-tasks)
- [Query For One Item](#query-for-one-item)
- [Coding the DELETE method](#coding-the-delete-method)
- [Hook It Up To The UI - Swipe to Delete a Tasks](#hook-it-up-to-the-ui---swipe-to-delete-a-tasks)
- [Coding the UPDATE method](#coding-the-update-method)
- [Coding the BULKINSERT method](#coding-the-bulkinsert-method)
- [Resources for this article](#resources-for-this-article)



## Main Structure of an Empty Content Provider
```java
/*
* Copyright (C) 2016 The Android Open Source Project
*
* Licensed under the Apache License, Version 2.0 (the "License");
* you may not use this file except in compliance with the License.
* You may obtain a copy of the License at
*
*      http://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

package com.example.android.todolist.data;

import android.content.ContentProvider;
import android.content.ContentValues;
import android.content.Context;
import android.content.UriMatcher;
import android.database.Cursor;
import android.net.Uri;
import android.support.annotation.NonNull;

// (1) Verify that TaskContentProvider extends from ContentProvider 
// and implements required methods
public class TaskContentProvider extends ContentProvider {

    /* onCreate() is where you should initialize anything you’ll need to setup
    your underlying data source.
    In this case, you’re working with a SQLite database, so you’ll need to
    initialize a DbHelper to gain access to it.
     */
    @Override
    public boolean onCreate() {
        // (2) Complete onCreate() and initialize a TaskDbhelper on startup
        // [Hint] Declare the DbHelper as a global variable

        return false;
    }

    @Override
    public Uri insert(@NonNull Uri uri, ContentValues values) {

        throw new UnsupportedOperationException("Not yet implemented");
    }

    @Override
    public Cursor query(@NonNull Uri uri, String[] projection, String selection,
                        String[] selectionArgs, String sortOrder) {

        throw new UnsupportedOperationException("Not yet implemented");
    }

    @Override
    public int delete(@NonNull Uri uri, String selection, String[] selectionArgs) {

        throw new UnsupportedOperationException("Not yet implemented");
    }

    @Override
    public int update(@NonNull Uri uri, ContentValues values, String selection,
                      String[] selectionArgs) {

        throw new UnsupportedOperationException("Not yet implemented");
    }

    @Override
    public String getType(@NonNull Uri uri) {

        throw new UnsupportedOperationException("Not yet implemented");
    }

}
```
#### [index](#index)

## Steps to Create a Provider

### 1st extend from the abstract content provider class
The first step is to create a new provider class that extends from the abstract content provider class that's part of the Android SDK and implement the onCreate method, which is called to initialize the Task Content Provider.

### 2nd register the provider in the Android Manifest file
The second step is to register this new provider in the Android Manifest file. Content providers need to be registered similar to activities. So that your app knows that the provider exists and knows how to refer to it by name and authority.

### 3th define URI's
The third step is to defife URIs that identify the TASK Content Provider and the different data types that it can return. These are needed so that later on a content resolver can find the provider and the specific data toy want to access just based on a given URI.

### 4th add these URI's to the Contract class
In the fourth step you'll update the task Contract Class to include URIs and string constants that will help you refer to the most commonly used URIs. 

### 5th build a URIMatcher
The fifth step in working with URIs is to build a URIMatcher to URI patterns to integers. This is a class that helps a content provider recognize and respond correctly to different types of URIs.

For example, it's often useful to use this matcher to distinguish between URIs that point to a large dataset, like multiple rows of task data. And URIs that point to a subset of that data, like data for an individual task.

### 6th implement the CRUD methods
The sixth and final step in the Task Content Provider class, you'll be implementing the required CRUD methods to access and change data. Methods like query and insert etc.

#### [index](#index)

## Build the Content Provider
You will use the structure of the Empty Content Provider and in the onCreate you should initialize anything you'll need to setup and access your underlying data source. In this case, the provider is working with an underlying SQLite database.
>Here is a good place to give it a way to open that database with the task db helper object.

First create a member variable name mTaskDbHelper for the TaskDbHelper, then in the onCreate you'll assign this to a new TaskDbHelper passing in the context.
```java
    // Member variable for a TaskDbHelper that's initialized in the onCreate() method
    private TaskDbHelper mTaskDbHelper;

    /* onCreate() is where you should initialize anything you’ll need to setup
    your underlying data source.
    In this case, you’re working with a SQLite database, so you’ll need to
    initialize a DbHelper to gain access to it.
     */
    @Override
    public boolean onCreate() {
        // get the Context which you have to import.
        Context context = getContext();
        // Create the DbHelper
        mTaskDbHelper = new TaskDbHelper(context);
        // return true because this method is done
        return true;
    }
```
After that you'll have to register it in the Android manifest.
```xml
        <provider
            android:name="com.example.android.todolist.data.TaskContentProvider"
            android:authorities="com.example.android.todolist"
            android:exported="false"/>
```

#### [index](#index)

## Define The URI Structure
You already know what the task data looks like and next you'll need to tell the content provider how to access that data. Earlier, when you communicated with the provider in the quiz example app, you saw how you always needed to make data request through a content resolver.

You'd call the correct action audit and pass in the URI for the data you wanted to access. 
> In general, URIs tell content resolver tow things.
* Identify your provider, which provider they want to talk to.
*  Identify different type of data that the provider can work with, what specific data is being requested.

So when you build your own content provider, it's up to you to define URIs that both, first identify your provider, and second, identify each different type of data that the provider can work with.

For our example the To Do List app, we want to access two different types of data.

* The first is a directory of tasks
* The second are individual tasks

This way, we can do things like display all if the tasks, and we cam also modify a task one at a time. So we'll define URIs to distinguish between these two types of data.

Now, there are quidelines for how content URIs are constructed. You've already seen a few examples of this structure. 

For example, you see that all content URIs start with a base that includes a scheme, followed by an authority. Then after that is the path, that indicates the specific data that you're intrested in. This is the portion of the URI that will help the provider select the correct data.
```xml
	<scheme>//<authority>/<path>
```	
And this is the part you'll need to define. So let's define the URI for accessing all of the task data. This directory is a SQL like table named tasks. For consistency, when you define the path for this specific URI, you should define it with the same name. And the final URI should look like this, the content scheme, authority, and then the tasks path at the end.
```java
	content://com.android.example.todolist/tasks
```
You have to create a provider for individual item in a directory, or for maybe a smaller subset of rows in the large directory. Well, in these cases, you need to include more inforamtion in the URI itself. For example in our tasks directory, we might want to see the data in the priority column only. Or perhaps we want to point to one row by its specific ID.

If I want to look only at the priority column, I might to find the URI that points to data by pending the name of the column to my path. 
```java
	content://com.udacity.example.todolist/tasks/priority
```
This path **tasks/priority** has the name of the task directory and the column priority. So it includes all of the information, a content provider would need, to select data from the priority column.

Similary if you wanted to design a URI fro looking at a single task, you could decide to have a URI that points to that row by appending its unique ID.
```java
	content://com.udacity.example.todolist/tasks/2
```
You could decide to have a URI that points to that row by appending its unique ID and the path would be **tasks/2**. For the task contenet provider to respond to a single row of data like this, you'll actually want to tell it when any URI is pointing to a single task by its ID.
```java
	content://com.udacity.example.todolist/tasks/1
	content://com.udacity.example.todolist/tasks/2
	content://com.udacity.example.todolist/tasks/3
```
To avoid defining a bunch of URIs, a single URI for every row in my table. You define a signle task using something called a wildcard character, the hash **#** symbol.
```java
	content://com.udacity.example.todolist/tasks/#
```
What this hash **#** symbol stands for is any integer number. So as long as the URI has a integer ID after its tasks path, it will be recognized as a URI that points to a single item.

Let's look a few more examples of URI structures that use these wildcard charachters. URIMatchers have matching capabilities similar to regex pattern matching, but it;s a quite simpler.

"path" - matches "path" **exactly**
"path/#" - matches "path" followed by a **number**
"path/*" - matches "path" followed by a **string**
"path/*/other/#" - matches "path" followed by a **string** followed by "other" followed by **number**

A URIMatcher recognizes only two wildcard charachters, an asterisk and that hash symbol. The hash **#** symbol can stand for an integer of any length. And the asterisk * stands in for a string of any length. And these symbols can be combined with each other or specific URI segments to define a complete URI structure.

These are two specific URIs that you've seen.
```java
	// points to the complete directory of tasks.
	content://com.android.todolist/tasks
	// points to a row in the directory with ID 2.
	content://com.android.todolist/tasks/2
	// points to a row where the # identifies, because of that symbol, 
	// any number will fit this pattern.
	content://com.android.todolist/tasks/#
	// there's also the asterisk, which stands in for any string
	content://com.android.todolist/tasks/*
	// this path structure will start with any string, then a segment named other, 
	// and then finally an integer at the end.
	content://com.android.todolist/tasks/*/other/#
```
#### [index](#index)

## URI Structure for the ToDo list
For the ToDo list app, we'll be interested in displaying the contents of a cursor that contains data for all out tasks. So we'll need to define a URI that identifies the entire tasks directory. Then, for doing things like deleting an individual task, we'll also want to define a URI that identifies a single row of data.
```java
	content://<authority>/tasks
	content://<authority>/tasks/#
```
Because 	all the URIs look pretty similar, with the same starting scheme and authority. And it's good practice to store any URI components that you commonly use as constants in the **contract class**.
> Remember, the **contract** is designed to keep track of constants that will help you access data in a given database.

So we'll add to it by including the most commonly used URIs and paths. As we've seen, to access data from the task content provider you need to define the following URIs.

![URIs to define](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/URIs%20to%20define.png)

One for the task directory and one for a single task.
```java
	// define the Scheme
	URI Scheme = content://
	// define the Content authority
	Content Authority = reference to the provider (com.exmaple.android.todolist)
	// define the base content URI wich is the scheme and the authority together.
	Base content URI = URI Scheme + Content Authority
	// After that, is a path, and paths point to specific table or file.
	Path = to specific data
	// And finally, inside your task entry class, you'll want to have a constant
	// for the most specific content URI that combines all of these elements.
	Content URI = base content URI + path
```	

### add the constants to the contract
```java
/*
* Copyright (C) 2016 The Android Open Source Project
*
* Licensed under the Apache License, Version 2.0 (the "License");
* you may not use this file except in compliance with the License.
* You may obtain a copy of the License at
*
*      http://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

package com.example.android.todolist.data;

import android.net.Uri;
import android.provider.BaseColumns;


public class TaskContract {

    /* Add content provider constants to the Contract
     Clients need to know how to access the task data, and it's your job to provide
     these content URI's for the path to that data:
        1) Content authority,
        2) Base content URI,
        3) Path(s) to the tasks directory
        4) Content URI for data in the TaskEntry class
    */
    // The authority, which is how your code knows which Content Provider to access
    // The authority is defined in the Android Manifest, so copy and paste it from there.
    public static final String AUTHORITY = "com.example.android.todolist";

    // The base content URI = "content://" + <authority>
    public static final Uri BASE_CONTENT_URI = Uri.parse("content://" + AUTHORITY);

    // Define the possible paths for accessing data in this contract
    // This is the path for the "tasks" directory, that will be appended 
    // to the base content URI
    public static final String PATH_TASKS = "tasks";
    // If the provider kept track of multiple tables of data, you would include multiple
    // corresponding path constants here.

// TaskEntry is an inner class that defines the contents of the task table     
public static final class TaskEntry implements BaseColumns {

        // Finally, inside this task entry class in the contract,
        // which keeps track of the data that a task entry contains,
        // you should create a complete URI for these entries as a constant
        // called Content URI.
        // TaskEntry content URI = base content URI + path
        public static final Uri CONTENT_URI =
                BASE_CONTENT_URI.buildUpon().appendPath(PATH_TASKS).build();
        // This final content URI will include the scheme, the authority, and our tasks path.

        // Task table and column names
        public static final String TABLE_NAME = "tasks";

        // Since TaskEntry implements the interface "BaseColumns", it has an 
        // automatically produced
        // "_ID" column in addition to the two below
        public static final String COLUMN_DESCRIPTION = "description";
        public static final String COLUMN_PRIORITY = "priority";


        /*
        The above table structure looks something like the sample table below.
        With the name of the table and columns on top, and potential contents in rows

        Note: Because this implements BaseColumns, the _id column is generated automatically

        tasks
         - - - - - - - - - - - - - - - - - - - - - -
        | _id  |    description     |    priority   |
         - - - - - - - - - - - - - - - - - - - - - -
        |  1   |  Complete lesson   |       1       |
         - - - - - - - - - - - - - - - - - - - - - -
        |  2   |    Go shopping     |       3       |
         - - - - - - - - - - - - - - - - - - - - - -
        ...
         - - - - - - - - - - - - - - - - - - - - - -
        | 43   |   Learn guitar     |       2       |
         - - - - - - - - - - - - - - - - - - - - - -
         */
    }
}
```
#### [index](#index)

## Build The URIMatcher

Now you have to find the two types of content URIs that the ToDo list app will use to do things like *insert and query data*.  The first type gives us access to a directory of tasks. And the second to a single task.

And you've added constants in your contract class to help you refer to these URIs. But how does this all fit in to how the provider works?

The provider has to have a way to recognixe the different URIs that are sent to it, and based on the path, match it with specific data. So whether it receives a URI for a large directory, or just a single row of data, it can interact with it correctly in each case.

> And guess what. **It's up to you to code this URI recognition.**

One way to recognize specific URIs would be look at the URI that's passed to your provider whenever data is accessed, parse it as a string and see if that string matches the format for a directory or a single row of data.

For example you could write an if statement like, if the UriString.is equal to ...
```java
if( UriString.equals("content://com.example.android.todolist/tasks") ) {
    // The URI identifies directory operations
    deleteAllTasks();
} else if( UriString.equals("content://com.example.android.todolist/tasks" + "some_ID?") ) {
    // The URI identifies single row operations
    deleteOneTask();
}
```
But this becomes tricky when you want to check if a string has an integer ID at the end. And these statemets can become really long and hard to read. But there's a better way to do this with something called a UriMatcher. The UriMatcher determines what kind of URI the provider receives and match it to an integer constant. So that you can easily make a switch statement instead of using a series of long if statments that check for equality.
 
 ### add to the Empty Content Provider code
 ```java
// Verify that TaskContentProvider extends from ContentProvider and implements required methods
public class TaskContentProvider extends ContentProvider {

    // (1) Define final integer constants for the directory of tasks and a single item.
    // It's convention to use 100, 200, 300, etc for directories,
    // and related ints (101, 102, ..) for items in that directory.
    public static final int TASKS = 100;
    public static final int TASKS_WITH_ID = 101;

    // Now let's actually build our UriMatcher and associate these constants with the correct URI.
    // (3) Declare a static variable for the Uri matcher that you construct
    private static final UriMatcher sUriMatcher = buildUriMatcher();
    // this is a member variable but starts with a lowercase s because it's static variable.

    // (2) Define a static buildUriMatcher method that associates URI's with their int match
    public static UriMatcher buildUriMatcher() {
        // first create a new UriMatcher object, by passing in the constant UriMatcher.nomatch
        final UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
        // Add the matches you want, by telling it which URI structures to recognize
        // and the integer constants they'll match with.
        // Using the method addURI(String authority, String path, int code)
        // directory
        uriMatcher.addURI(TaskContract.AUTHORITY, TaskContract.PATH_TASKS, TASKS);
        // single item
        uriMatcher.addURI(TaskContract.AUTHORITY, TaskContract.PATH_TASKS + "/#", TASKS_WITH_ID);

        return uriMatcher;
    }
.....
}
```
For example after creating the UriMatcher, you can call UriMatcher.match and pass in a URI. The match method returns an int value which will be the integer that you associated with the passed in URI structure using the add URI method. 
```java
    int match = sUriMatcher.match(uri);
```
And once you have this match number, you can write a switch statment to programm different behavior for a match equal to tasks.
```java
    switch (match) {
        // handle different integer matches
        case TASKS:
            // Handle directory task case
            retCursor = db.query(TABLE_NAME, projection, 
                selection, slectionsArgs, null, null, sortOrder);
            break;
        case TASK_WITH_ID:
            // Handle single task case
            break;
        default:
            throw new UnsupportedOperationException("Unknown uri: " + uri);
    }
```
### Example of Using the delete function on a ContentResolver
```java
    // The constructed URI will be:
    // content://com.example.android.todolist/tasks/4.
    // The URIMatcher will recognize this URI as identifying a single row
    // in the tasks database with a row id = 4, so it will match it to
    // the TASK_WITH_ID integer constant
    int id = 4;
    String idString = "" + id;
    Uri uri = TaskContract.TaskEntry.CONTENT_URI;
    uri = uri.buildUpon().appendPath(idString).build();

    // This delete call will get to our TaskContentProvider and then
    // the UriMatcher will match the passed in URI to the TASK_WITH_ID
    // integer constant. This is important for the delete method because
    // the ContentProvider needs to be able to identify and delete just
    // the one row with ID = 4. It shouldn’t delete the entire directory
    // of tasks.
    getContentResolver().delete(uri, null, null);
```
### Example of different delete behavior depending on the recognized URI.
![Different delete behavior depending on URI](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/Different%20delete%20behavior%20depending%20on%20URI.png)

#### [index](#index)

## Resolver To Database Flow
Now that you've build a URIMatcher, let's go through the detailed, end-toend flow for how data moves from the task database to the ToDo list user interface. Let's say that your app is querying for data to display in the UI. first from the UI code the app will get a content Resolver and call query on it, passing in the URI for the exact provider and data you want to read.

The the Resolver finds the correct task content provider based on the authority of the passed in URI and passes on the query. The Provider's query function will then use the URIMatcher you've built to decide how to react to the passed in URI and determine what kind of data to retrieve. Whether that's one row of data or the directory of all tasks or some other specific selection. Base on the match, the query function will basically translate the URI and other parameters into the correct SQL code for selected data. So, using the Matcher and the code for your query, the provider will then retrive the desired data from the underlying database.

![Resolver To Database Flow](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/Resolver%20To%20Database%20Flow.png)

Then the call travels all the way back to the Resolver in your UI code and returns a cursor wuth that data, and this is how all calls from your UI to the database will function, passing through the provider to reach the database.

#### [index](#index)

## Overview Of Provider Methods
There are six methods that every content provider is required yo have.
* onCreate which initializes the provider, and which you've already implemented.
* four so-called CRUD Methods, where CRUD stands for Create, Read, Update and Delete. And these are the four main functions used for peristent data storage. This will be pretty similar to the CRUD functions you implemented in the SQLite lesson.
* getType which returns the MIME type of the content being returned. And a MIME type is just a way of identifying what format the content is in, in a similar way to file types.
```java
String getType(Uri uri)
```

#### [index](#index)

### The CRUD methods
**insert**, To let users of your app create new data, you need to code the content provider's insert method.  This will take in a content Uri which tells the correct directory to insert data into, and a ContentValues object that contains the new data to insert. After the data us inserted, this returns a newly created content Uri that tells you the location of the inserted data.
```java
Uri insert(Uri uri, ContentValues values)
```
**query**, To read data and display it in your UI, you'll write the query method which asks for data from your content provider. This returns a cursir that contains a row, or rows, of data that th query has asked for.
```java
Cursor query (Uri uri, String[] projection, 
        String selection, String[] selectionArgs, String sortOrder)
```
**update**,  This takes the same parameters as entered, so it know where to update data by the Uri, and with what ContentValues. And this will return an integer value for the number of rows that were updated.
```java
int update(Uri uri, ContentValues values, String selection, String[] selectionArgs)
```
**delete**, The delete method needs to know the Uri that points to the row, or rows, to delete. And this should return the number of rows deleted.
```java
int delete(Uri uri, String selection, String[] selectionsArgs)
```

#### [index](#index)

## Coding the INSERT method
With the insert method we can add data to our task database. Our end goal for implementing this function is to hook it up to our add task activity UI, so then when a user types in a new task with a selected piority and clicks the add button, this will insert newly created data into our task database. One thing that the provider will do differently than straight up SQL code is to have different actions for different URIs. In this way, it can do some simple data validation by making sure it only responds to valid URIs.

When you insert data, you'll be inserting a new row of data into the entire tasks directory, which holds all of our existing data. So you'll want your content provider to respond only to the URI that identifies that directory. And to handle this case, you'll use your URI matcher and a switch statement.

```java
@Override
    public Uri insert(@NonNull Uri uri, ContentValues values) {
        // (1) Get access to the task database (to write new data to)
        // So that we can write new data to it, we'll use mTaskDbHelper.getWritableDatabase()
        final SQLiteDatabase db = mTaskDbHelper.getWritableDatabase();

        // (2) Write URI matching code to identify the match for the tasks directory
        // This match will be either 100 for all tasks or 101 for a task with ID, or an unrecognized URI
        int match = sUriMatcher.match(uri);

        // (3) Insert new values into the database
        // (4) Set the value for the returnedUri and write the default case for unknown URI's

        Uri returnUri;

        // We want to check these cases with a switch case and respond to only the tasks case.
        // If the tasks case is met, we can insert a new row of data into this directory.
        // We can't insert data into just one row like in the task with id case.
        // And if we receive any other type URI or an invalid one, the default behavior
        // will be to throw an UnsupportedOperationException and print out an
        // Unknown uri message.
        switch(match) {
            case TASKS:
                // We'll insert new data into the tasks directory by calling insert om our database.
                // Inserting values into tasks table
                long id = db.insert(TaskContract.TaskEntry.TABLE_NAME, null, values);
                // If the insert wasn't successful, this ID will be -1
                // But if ths insert is successful, we want the provider's insert method to take
                // that unique row ID and create and return a URI for that newly inserted data.

                // So first, let's write an if that checks that this insert was successful.
                if ( id > 0 ) {
                    // Success, the insert worked and we can construct the new URI
                    // that will be our main content URI, which has the authority
                    // and tasks path, with the id appended to it.
                    returnUri = ContentUris.withAppendedId(TaskContract.TaskEntry.CONTENT_URI, id);
                    // contentUris is an Android class that contains helper methods for
                    // constructing URIs
                } else {
                    // Otherwise, we'll throw a SQLiteException, because the insert failed.
                    throw new android.database.SQLException("Failed to insert row into " + uri);
                }

                break;
            // Default case throws an UnsupportedOperationException
            default:
                throw new UnsupportedOperationException("Unknown uri: " + uri);
        }

        // (5) Notify the resolver if the uri has been changed, and return the newly inserted URI
        // To notify the resolver that a change has occurred at this particular URI,
        // you'll do this using the notify change function.
        // This is so that the resolver knows that something jas changed, and
        // can update the database and any associated UI accordingly
        getContext().getContentResolver().notifyChange(Uri, null);

        return returnUri;
    }
```
#### [index](#index)

### Hook It Up To The UI
Now that we've coded the insert method in our provider class, we can call it from the AddTaskActivity using a content resolver. We'll want to link a call to the insert method to this add button, so that it's called whenever a user inputs a task and clicks add.
```java
    /**
     * onClickAddTask is called when the "ADD" button is clicked.
     * It retrieves user input and inserts that new task data into the underlying database.
     */
    public void onClickAddTask(View view) {
        // Not yet implemented
        // (6) Check if EditText is empty, if not retrieve input and store it 
        // in a ContentValues object
        // To retrieve a user's input description, we have to retrieve the text they
        // entered into the edit text view in the add task layout.
        String input = ((EditText) 
            findViewById(R.id.editTextTaskDescription)).getText().toString();

        // We don't want any empty tasks being created
        // If someone presses the add button without typing in a task to do.
        if (input.length() == 0) {
            return;
        }

        // We'll create a new ContentValues object to place this task data into.
        ContentValues contentValues = new ContentValues();

        // Put the task description and selected mPriority into the ContentValues
        contentValues.put(TaskContract.TaskEntry.COLUMN_DESCRIPTION, input);
        contentValues.put(TaskContract.TaskEntry.COLUMN_PRIORITY, mPriority);

        // (7) Insert new task data via a ContentResolver
        // Then we need to insert these values into our database with
        // a call to a content resolver
        Uri uri = getContentResolver().insert(TaskContract.TaskEntry.CONTENT_URI, contentValues);

        // This insert method should return a URI if it works.

        // (8) Display the URI that's returned with a Toast
        // [Hint] Don't forget to call finish() to return to MainActivity 
        // after this insert is complete
        if (uri != null) {
            Toast.makeText(getBaseContext(), uri.toString(), Toast.LENGTH_LONG).show();
        }

        // call finish() to tell that this activity is over and so
        // we should return to the main activity after an insert is complete.
        finish();
    }
```

#### [index](#index)

## What Does the CustomCursorAdapter do?
The **CustomCursorAdapter** will inflate views using the xml layout file **task_layout**, and create **ViewHolders** that will fill the main **RecyclerView**.

Each **ViewHolder** includes data about a single task: it’s text description and priority level. The **priorityView** will actually be a small colored circle that indicates the priority level 1-3 (1 is high and 3 is low).

The priority circle is a drawable resource, and its color is assigned to red, yellow, or green based on the priority level.

All of this code was included in your starter code, so no need to change anything in here.

#### [index](#index)

## Coding the QUERY method
Query lets us ask for rows of data so that you can display them in the UI. From our description of the content provider methods, you know that the query function takes in a URI and these other inputs to select data from a database. And then it should return a cursor that contains the desired data. 

The most important parameter is the URI which should point to our tasks directory because we will want to display all of the tasks we've made.

In our task content provider class, we'll start our query method similar to how we started our intent method.

```java
    @Override
    public Cursor query(@NonNull Uri uri, String[] projection, String selection,
                        String[] selectionArgs, String sortOrder) {

        // (1) Get access to underlying database (read-only for query)
        // First by gaining access to our underlying database with a call
        // to getReadableDatabase since we're just reading from it and not
        // changing it.
        final SQLiteDatabase db = mTaskDbHelper.getReadableDatabase();

        // (2) Write URI match code and set a variable to return a Cursor
        // Using our URI matcher to get a match number that identifies the passed in URI
        int match = sUriMatcher.match(uri);

        // (3) Query for the tasks directory and write a default case
        // We know the method will be returning the cursor
        Cursor retCursor;

        // we write a switch statement to manage data correctly in the case we want.
        switch (match) {
            // Query for the tasks directory
            case TASKS:
                // This starting code will look pretty similar for all of our CRUD functions.
                // The query for our tasks case, will return all the rows in our database 
                // as a cursor.
                retCursor = db.query(TaskContract.TaskEntry.TABLE_NAME,
                        projection, selection, selectionArgs, null, null, sortOrder);
                break;
            // default exception
            default:
                throw new UnsupportedOperationException("Unknown uri: " + uri);
        }

        // (4) Set a notification URI on the Cursor to tell the cursor
        // what content URI it was created for
        retCursor.setNotificationUri(getContext().getContentResolver(), uri);
        // (5) and return that Cursor
        return retCursor;
    }
```

#### [index](#index)

### Hook It Up To The UI - Display the Tasks
You already know that when you query for data, you have to do so asynchronously. Which means you have to retrive the data on a background thread. This is because a query can be unreliable or slow, and you want to make sure that your main UI thread runs quickly and smoothly.

![Asynchronously Threads](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/Asynchronously%20Threads.png)

So to handle queries for our task data, we're going to be using an AsyncTask Loader. This loader implements call back methods to start loading data, perform a query in the background, and finally return that data.

We can also tell our loader to requery the task data whenever it changes, like when we insert a new task.

In fact, in our main activity code, there's already a requery that happens in the on resume method. In the on resume method, we restart the loader so that any time you leave the main activity and return, like when you go to AddTaskActivity to insert a new task, the loader will restart and update the UI.

So all we need to do now is add some simple code to load our task data on a background thread.

In the **MainActivity.java** to the loader callback method **loadInBackground()** 
```java
// MainActivity.java
/*
* Copyright (C) 2016 The Android Open Source Project
*
* Licensed under the Apache License, Version 2.0 (the "License");
* you may not use this file except in compliance with the License.
* You may obtain a copy of the License at
*
*      http://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

package com.example.android.todolist;

import android.content.Intent;
import android.database.Cursor;
import android.os.Bundle;
import android.support.design.widget.FloatingActionButton;
import android.support.v4.app.LoaderManager;
import android.support.v4.content.AsyncTaskLoader;
import android.support.v4.content.Loader;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;
import android.support.v7.widget.helper.ItemTouchHelper;
import android.util.Log;
import android.view.View;

import com.example.android.todolist.data.TaskContract;


public class MainActivity extends AppCompatActivity implements
        LoaderManager.LoaderCallbacks<Cursor> {


    // Constants for logging and referring to a unique loader
    private static final String TAG = MainActivity.class.getSimpleName();
    private static final int TASK_LOADER_ID = 0;

    // Member variables for the adapter and RecyclerView
    private CustomCursorAdapter mAdapter;
    RecyclerView mRecyclerView;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Set the RecyclerView to its corresponding view
        mRecyclerView = (RecyclerView) findViewById(R.id.recyclerViewTasks);

        // Set the layout for the RecyclerView to be a linear layout, which measures and
        // positions items within a RecyclerView into a linear list
        mRecyclerView.setLayoutManager(new LinearLayoutManager(this));

        // Initialize the adapter and attach it to the RecyclerView
        mAdapter = new CustomCursorAdapter(this);
        mRecyclerView.setAdapter(mAdapter);

        /*
         Add a touch helper to the RecyclerView to recognize when a user swipes 
         to delete an item.
         An ItemTouchHelper enables touch behavior (like swipe and move) on each ViewHolder,
         and uses callbacks to signal when a user is performing these actions.
         */
        new ItemTouchHelper(new ItemTouchHelper.SimpleCallback(0, 
                    ItemTouchHelper.LEFT | ItemTouchHelper.RIGHT) {
            @Override
            public boolean onMove(RecyclerView recyclerView, 
                    RecyclerView.ViewHolder viewHolder, RecyclerView.ViewHolder target) {
                return false;
            }

            // Called when a user swipes left or right on a ViewHolder
            @Override
            public void onSwiped(RecyclerView.ViewHolder viewHolder, int swipeDir) {
                // Here is where you'll implement swipe to delete
            }
        }).attachToRecyclerView(mRecyclerView);

        /*
         Set the Floating Action Button (FAB) to its corresponding View.
         Attach an OnClickListener to it, so that when it's clicked, 
         a new intent will be created to launch the AddTaskActivity.
         */
        FloatingActionButton fabButton = (FloatingActionButton) findViewById(R.id.fab);

        fabButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                // Create a new intent to start an AddTaskActivity
                Intent addTaskIntent = new Intent(MainActivity.this, AddTaskActivity.class);
                startActivity(addTaskIntent);
            }
        });

        /*
         Ensure a loader is initialized and active. If the loader doesn't already exist, 
         one is created, otherwise the last created loader is re-used.
         */
        getSupportLoaderManager().initLoader(TASK_LOADER_ID, null, this);
    }


    /**
     * This method is called after this activity has been paused or restarted.
     * Often, this is after new data has been inserted through an AddTaskActivity,
     * so this restarts the loader to re-query the underlying data for any changes.
     */
    @Override
    protected void onResume() {
        super.onResume();

        // re-queries for all tasks
        // we restart the loader so that any time you leave the main activity and return,
        // like when you go to AddTaskActivity to insert a new task, the loader will
        // restart and update the UI.
        getSupportLoaderManager().restartLoader(TASK_LOADER_ID, null, this);
    }


    /**
     * Instantiates and returns a new AsyncTaskLoader with the given ID.
     * This loader will return task data as a Cursor or null if an error occurs.
     *
     * Implements the required callbacks to take care of loading data at all stages of loading.
     */
    @Override
    public Loader<Cursor> onCreateLoader(int id, final Bundle loaderArgs) {

        return new AsyncTaskLoader<Cursor>(this) {

            // Initialize a Cursor, this will hold all the task data
            Cursor mTaskData = null;

            // onStartLoading() is called when a loader first starts loading data
            @Override
            protected void onStartLoading() {
                if (mTaskData != null) {
                    // Delivers any previously loaded data immediately
                    deliverResult(mTaskData);
                } else {
                    // Force a new load
                    forceLoad();
                }
            }

            // loadInBackground() performs asynchronous loading of data
            @Override
            public Cursor loadInBackground() {
                // Will implement to load data

                // (5) Query and load all task data in the background; sort by priority
                // [Hint] use a try/catch block to catch any errors in loading data
                // We use a content resolver to query for the tasks directory.
                // We'll surround our query in a try-catch, which menas we'll try to
                // query and catch any error that may occur while loading data.
                try {
                    // We'll try to return a cursor of data.
                    // We'll call query on the content resolver and
                    // pass in the CONTENT_URI that points to our tasks directory.
                    // The projection and selection arguments will all be null, since by default,
                    // null will select all the data.
                    // The last argument we're going to pass in will be the sort order
                    // And we actually want to sort by priority, so that the higher
                    // priority tasks show up at the top of our list.
                    // So we'll put in COLUMN_PRIORITY as this last argument.
                    // After this data is loaded, this callback method returns this cursor
                    // And this cursor will actually be passed to our custom cursor adapter,
                    // which creates the task views in the main recycler view.
                    return getContentResolver().query(TaskContract.TaskEntry.CONTENT_URI,
                            null, null, null,
                            TaskContract.TaskEntry.COLUMN_PRIORITY);

                } catch (Exception e) {
                    // Just log any errors if the data fails to load, and return a null cursor
                    Log.e(TAG, "Failed to asynchronously loada data.");
                    e.printStackTrace();
                    return null;
                }
            }

            // deliverResult sends the result of the load, a Cursor, to the registered listener
            public void deliverResult(Cursor data) {
                mTaskData = data;
                super.deliverResult(data);
            }
        };

    }


    /**
     * Called when a previously created loader has finished its load.
     *
     * @param loader The Loader that has finished.
     * @param data The data generated by the Loader.
     */
    @Override
    public void onLoadFinished(Loader<Cursor> loader, Cursor data) {
        // Update the data that the adapter uses to create ViewHolders
        mAdapter.swapCursor(data);
    }


    /**
     * Called when a previously created loader is being reset, and thus
     * making its data unavailable.
     * onLoaderReset removes any references this activity had to the loader's data.
     *
     * @param loader The Loader that is being reset.
     */
    @Override
    public void onLoaderReset(Loader<Cursor> loader) {
            mAdapter.swapCursor(null);
    }

}
```
#### [index](#index)

## Query For One Item
Let's see how to modify the query code to retrive just one row of data. Let's think about this in terms of SQL code. To select all the data in a table you write a select all query. And to narrow down the number of rows that are returned, you can use a WHERE clause.

A WHERE clause retrieves data only when it fits some criteria. For tasks, this could be only tasks with priority equal to one.
```sql
SELECT * FROM tasks_table WHERE priority = 1
```
And to retrieve a single row of data, you habe to ask for it, with a unique criteria which is its id.
```sql
SELECT * FROM tasks_table WHERE id= 2
```
To doing the same thing using only our provider query, the provider will translate method calls into the correct SQL code to interact with data, and to construct a WHERE clause it uses the selection and selectionsArgs parameters of its query method.

![Selection and selectionsArgs parameters](https://raw.githubusercontent.com/kalxasath/android-dev-challenge-2017/master/assets/Selection%20and%20selectionsArgs%20parameters.png)

```java
// Implement query to handle requests for data by URI
    @Override
    public Cursor query(@NonNull Uri uri, String[] projection, 
                        String selection,String[] selectionArgs, 
                        String sortOrder) {

        // Get access to underlying database (read-only for query)
        final SQLiteDatabase db = mTaskDbHelper.getReadableDatabase();

        // Write URI match code
        // Write a query for the tasks directory and default case

        int match = sUriMatcher.match(uri);
        Cursor retCursor;

        switch (match) {
            // Query for the tasks directory
            case TASKS:
                retCursor =  db.query(TABLE_NAME,
                        projection,
                        selection,
                        selectionArgs,
                        null,
                        null,
                        sortOrder);
                break;

            // Add a case to query for a single row of data by ID
            // Use selections and selectionArgs to filter for that ID
            case TASK_WITH_ID:
                // To Select a row of data by its ID, we'll use the selection and
                // selection args parameters of the query method.
                // First we'll have to get the row ID from the past URI.
                // The URI will look similar to the tasks directory URI.
                // It'll start with the same scheme authority and tasks path.
                // But this time also with an ID as the part of the path.
                // And we can grab this ID by Using a call to get path segments on that URI.
                // And get with the index 1 passed in.
                // Index 0 would be the tasks portion of the path.
                // And index 1 is the segment right next to that.
                // Get the id from the URI
                String id = uri.getPathSegments().get(1);

                // Selection is the _ID column = ?, 
                // and the Selection args = the row ID from the URI
                // The question mark indicates that this is asking for
                String mSelection = "_id=?";
 
                // the rest of this equality from the selection args parameter.
                // And the selection args should be the row ID 
                // which we just got from the past URI.
                // And selection args has to be an array of strings.
                String[] mSelectionArgs = new String[]{id};

                // Construct a query as you would normally, passing in the selection/args
                retCursor =  db.query(TABLE_NAME,
                        projection,
                        mSelection,
                        mSelectionArgs,
                        null,
                        null,
                        sortOrder);
                break;

            // Default exception
            default:
                throw new UnsupportedOperationException("Unknown uri: " + uri);
        }

        // Set a notification URI on the Cursor
        retCursor.setNotificationUri(getContext().getContentResolver(), uri);

        // Return the desired Cursor
        return retCursor;
    }
```
#### [index](#index)

## Coding the DELETE method

Looking at our list of CRUD functions, we see that delete function takes in a URI and also an optional selection and selection args as parameters. So it does look a little bit like the query function which we're familiar with, and so it's going to be pretty similar to implement.

In the delete method, you should be doing four things.
1. Access the database and write URI matching code.
2. Use selection and selectionsArgs to delete one row of data.
3. Don't forget to notify the Content Resolver of any change after deletion.
4. Return the number of deleted item as an integer.

```java
    @Override
    public int delete(@NonNull Uri uri, String selection, String[] selectionArgs) {

        // (1) Get access to the database and write URI matching code 
        // to recognize a single item
        final SQLiteDatabase db = mTaskDbHelper.getWritableDatabase();

        int match = sUriMatcher.match(uri);
        int retDeletedRecord = 0; // starts as 0

        // COMPLETED (2) Write the code to delete a single row of data
        // [Hint] Use selections to delete an item by its row ID
        switch (match) {
            case TASK_WITH_ID:
                // To Delete a row of data by its ID, we'll use the selection and
                // selection args parameters of the delete method.
                // First we'll have to get the row ID from the past URI.
                // The URI will look similar to the tasks directory URI.
                // It'll start with the same scheme authority and tasks path.
                // But this time also with an ID as the part of the path.
                // And we can grab this ID by Using a call to get path segments on that URI.
                // And get with the index 1 passed in.
                // Index 0 would be the tasks portion of the path.
                // And index 1 is the segment right next to that.
                // Get the id from the URI
                String id = uri.getPathSegments().get(1);

                // Selection is the _ID column = ?, and the Selection args = the row ID 
                // from the URI
                // The question mark indicates that this is asking for
                String mSelection = "_id=?";

                // the rest of this equality from the selection args parameter.
                // And the selection args should be the row ID which we just got from 
                // the past URI.
                // And selection args has to be an array of strings.
                String[] mSelectionArgs = new String[]{id};

                // delete the desired record by passing in the selection/args
                // return the number of rows deleted
                retDeletedRecord =  db.delete(TABLE_NAME,
                        mSelection,
                        mSelectionArgs);
                break;
            default:
                throw new UnsupportedOperationException("Unknown uri: " + uri);
        }

        // COMPLETED (3) Notify the resolver of a change and return the number of items deleted
        if (retDeletedRecord > 0) {
            // Task was deleted, set notification
            getContext().getContentResolver().notifyChange(uri, null);
        }

        return retDeletedRecord;
    }
```

#### [index](#index)

### Hook It Up To The UI - Swipe to Delete a Tasks

In the method onCreate()  we have the method ItemTouchHelper() as an item touch hopper, attached to the RecyclerView. The important function is onSwipe(), which is called whenever a user swipes left to right of the screen.
In the **MainActivity.java** in the **onCreate()** method implement in the **touch helper** in his **onSwiped()** method the deletion.
```java
        /*
         Add a touch helper to the RecyclerView to recognize when a user swipes 
         to delete an item.
         An ItemTouchHelper enables touch behavior (like swipe and move) on each ViewHolder,
         and uses callbacks to signal when a user is performing these actions.
         */
        new ItemTouchHelper(new ItemTouchHelper.SimpleCallback(0, 
                    ItemTouchHelper.LEFT | ItemTouchHelper.RIGHT) {
            @Override
            public boolean onMove(RecyclerView recyclerView, 
                    RecyclerView.ViewHolder viewHolder, RecyclerView.ViewHolder target) {
                return false;
            }

            // Called when a user swipes left or right on a ViewHolder
            @Override
            public void onSwiped(RecyclerView.ViewHolder viewHolder, int swipeDir) {
                // Here is where you'll implement swipe to delete
                // Here you are going to start by getting the ID for
                // the item that's being swiped in our RecyclerView
                // The value of this tag was set in the adapter class
                // to be the ID of the view.
                int id = (int) viewHolder.itemView.getTag();

                // (1) Construct the URI for the item to delete
                //[Hint] Use getTag (from the adapter code) to get the id of the swiped item
                // Then we're going to construct the uri that points to the specific task.
                String stringId = Integer.toString(id);
                // or
                stringId = "" + id;
                Uri uri = TaskContract.TaskEntry.CONTENT_URI;
                uri = uri.buildUpon().appendPath(stringId).build();

                // (2) Delete a single row of data using a ContentResolver
                getContentResolver().delete(uri, null, null);

                // (3) Restart the loader to re-query for all tasks after a deletion
                getSupportLoaderManager().restartLoader(TASK_LOADER_ID, null, 
                    MainActivity.this);
                
            }
        }).attachToRecyclerView(mRecyclerView);
```

#### [index](#index)

## Coding the UPDATE method

The sample code for update is below. We won't be using this functionality in this app, but if you plan to provide your app's data to other applications, you should implement all of the operations that you want developers to be able to use.

```java
    // Update won't be used in the final ToDoList app but is implemented here for completeness
    // This updates a single item (by it's ID) in the tasks directory
    @Override
    public int update(@NonNull Uri uri, ContentValues values, 
                String selection, String[] selectionArgs) {

        // Keep track of if an update occurs
        int tasksUpdated;

        // match code
        int match = sUriMatcher.match(uri);

        switch (match) {
            case TASK_WITH_ID:
                //update a single task by getting the id
                String id = uri.getPathSegments().get(1);
                //using selections
                tasksUpdated = mTaskDbHelper
                    .getWritableDatabase()
                    .update(TaskContract.TaskEntry.TABLE_NAME, 
                                values, "_id=?", new String[]{id});
                break;
            default:
                throw new UnsupportedOperationException("Unknown uri: " + uri);
        }

        if (tasksUpdated != 0) {
            //set notifications if a task was updated
            getContext().getContentResolver().notifyChange(uri, null);
        }

        // return number of tasks updated
        return tasksUpdated;
    }
```

The last method that every provider requires is called GetType. It returns a string that tells you the MIME type of the data that's being accessed. A MIME type is just a standard way to define a data type. Similar to file types like JPEG or plain text. For our purposes, it's okay to leave this method blank. Because we won't actually be using it in our app.

It only becomes useful when MIME types are used to organize data or to validate data. For example, an app might call getType to distinguish between movie and image types or to check data type before doing something like a file conversion. 

For our task data, the MIME types we care about are, an item for a single row of data, and a directory for multiple rows of data. 

Here's an example of getType for the ToDo list app:

```java
/* getType() handles requests for the MIME type of data
We are working with two types of data:
1) a directory and 2) a single row of data.
This method will not be used in our app, but gives a way to standardize the data formats
that your provider accesses, and this can be useful for data organization.
For now, this method will not be used but will be provided for completeness.
 */
@Override
public String getType(@NonNull Uri uri) {
    int match = sUriMatcher.match(uri);

    switch (match) {
        case TASKS:
            // directory
            return "vnd.android.cursor.dir" + "/" + TaskContract.AUTHORITY + "/" 
                + TaskContract.PATH_TASKS;
        case TASK_WITH_ID:
            // single item type
            return "vnd.android.cursor.item" + "/" + TaskContract.AUTHORITY + "/" 
                + TaskContract.PATH_TASKS;
        default:
            throw new UnsupportedOperationException("Unknown uri: " + uri);
    }
}
```
#### [index](#index)

## Coding the BULKINSERT method

Instead of one insert at a time, we'll be doing a bulk insert, which basically tries to insert an array of content values, not just one set, into a database. It will respond to the URI that identifies the directory of "weather" data. And then iterate through the array of values and enter each one.

We'll iverride the **bulkInsert()** method in the weather provider class. This method takes in the URI in an array of content values and it returns the number of values inserted.

```java
    /**
     * Handles requests to insert a set of new rows. In Sunshine, we are only going to be
     * inserting multiple rows of data at a time from a weather forecast. 
     * There is no use case
     * for inserting a single row of data into our ContentProvider, 
     * and so we are only going to
     * implement bulkInsert. In a normal ContentProvider's implementation, 
     * you will probably want
     * to provide proper functionality for the insert method as well.
     *
     * @param uri    The content:// URI of the insertion request.
     * @param values An array of sets of column_name/value pairs to add to the database.
     *               This must not be {@code null}.
     *
     * @return The number of values that were inserted.
     */
    @Override
    public int bulkInsert(@NonNull Uri uri, @NonNull ContentValues[] values) {

        // The bulkInsert will start with the same database accessing and
        // and URI match code that you've seen before
        final SQLiteDatabase db = mOpenHelper.getWritableDatabase();
        int match = sUriMatcher.match(uri);

        // (2) Only perform our implementation of bulkInsert if the URI matches 
        // the CODE_WEATHER code

        switch(match) {
            case CODE_WEATHER:
                // In this time, inside our weather directory case we'll
                // also include a database transaction.
                // A transaction is a way to mark the start and end of a large
                // data transfer. So you want to start a transaction, then try to
                // do all your inserts and finally end the transaction.
                // So here we begin the transaction and also initialize the number
                // of row inserted to 0.

                db.beginTransaction();
                int rowsInserted = 0;

                // To try inserting and then end the transaction we'll use
                // a try statement to do our inserts and then something
                // called a finally block of code.

                try {
                    // try to insert all data
                    // loop through all our content values
                    for (ContentValues value: values) {
                        // we'll insert each days weather into the underlying database 
                        // using db.insert.
                        long weatherDate = value
                            .getAsLong(WeatherContract.WeatherEntry.COLUMN_DATE);

                        if (!SunshineDateUtils.isDateNormalized(weatherDate)) {
                            throw new IllegalArgumentException(
                                "Date must be normalized to insert");
                        }

                        long _id = db.insert(WeatherContract.WeatherEntry.TABLE_NAME, 
                            null, value);
                        if (_id != -1) {
                            // if an insert is successful we'll update 
                            // the number of rows inserted.
                            rowsInserted++;
                        }
                    }
                    
                        // after the for loop is completed, 
                        // we set the transaction state to be successful.
                        db.setTransactionSuccessful();
                  
                } finally {
                    // execute after the try is complete
                    // here we'll just end the transaction.
                    db.endTransaction();
                }
                // (3) Return the number of rows inserted from our 
                // implementation of bulkInsert
                
                // we'll notify the content resolver that a change has 
                // been made to the directory URI.
                
                // we'll do this with a call to notify change
                if(rowsInserted > 0) {
                    getContext().getContentResolver().notifyChange(uri, null);
                }
                // return the number of rows that were inserted.
                return rowsInserted;

            default:
                // (4) If the URI does match match CODE_WEATHER, 
                // return the super implementation of bulkInsert
                // just return the super implementation of bulk insert, this is the
                // implementation in Android's content provider class that this
                // weather provider extends from.
                return super.bulkInsert(uri, values);
        }
```
> Important note for the deletion: If we want to delete the whole table rows and want to know how many rows was deleted, then we have to passing "1" for the selection.

```java
        /*
         * If we pass null as the selection to SQLiteDatabase#delete, our entire table will be
         * deleted. However, if we do pass null and delete all of the rows in the table, we won't
         * know how many rows were deleted. According to the documentation for SQLiteDatabase,
         * passing "1" for the selection will delete all rows and return the number of rows
         * deleted, which is what the caller of this method expects.
         */
        if (null == selection) selection = "1";
```

#### [index](#index)



 ## Resources for this article
- Lesson 10 - Building a Content Provider
- [The complete code for the Building a Content Provider](https://github.com/udacity/ud851-Exercises/tree/student/Lesson09-ToDo-List/T09.07-Solution-SwipeToDelete)
- [The code for the bulkInsert for Weather](https://github.com/udacity/ud851-Sunshine/tree/student/S09.02-Solution-ContentProviderBulkInsert)


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
