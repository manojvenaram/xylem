# xylem
## MainActivity.java
```
package com.example.sqlitedatebase;

import android.annotation.SuppressLint;
import android.content.ContentValues;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {
    private EditText inputEditText;
    private Button submitButton;
    private TextView displayTextView;
    private MyDatabaseHelper dbHelper;

    @SuppressLint("MissingInflatedId")
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        inputEditText = findViewById(R.id.inputEditText);
        submitButton = findViewById(R.id.submitButton);
        displayTextView = findViewById(R.id.displayTextView);

        dbHelper = new MyDatabaseHelper(this);
        SQLiteDatabase db = dbHelper.getWritableDatabase();

        submitButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String userInput = inputEditText.getText().toString();

                // Insert user input into the database table
                ContentValues values = new ContentValues();
                values.put("name", userInput);
                long newRowId = db.insert("mytable", null, values);

                // Display the data from the table
                String selectQuery = "SELECT * FROM mytable";
                Cursor cursor = db.rawQuery(selectQuery, null);

                StringBuilder stringBuilder = new StringBuilder();
                while (cursor.moveToNext()) {
                    @SuppressLint("Range") int id = cursor.getInt(((Cursor) cursor).getColumnIndex("id"));
                    @SuppressLint("Range") String name = cursor.getString(cursor.getColumnIndex("name"));
                    stringBuilder.append("ID: ").append(id).append(", Name: ").append(name).append("\n");
                }

                displayTextView.setText(stringBuilder.toString());

                cursor.close();
            }
        });
    }
}
```
## Activitymain.xml
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/inputEditText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter your name"
        android:inputType="text" />

    <Button
        android:id="@+id/submitButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Submit" />

    <TextView
        android:id="@+id/displayTextView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="18sp" />

</LinearLayout>
```
## DatabaseHelper.java
```
package com.example.sqlitedatebase;

import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

public class MyDatabaseHelper extends SQLiteOpenHelper {
    private static final String DATABASE_NAME = "mydatabase.db";
    private static final int DATABASE_VERSION = 1;

    public MyDatabaseHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        // Create your table
        String createTableQuery = "CREATE TABLE mytable (id INTEGER PRIMARY KEY, name TEXT)";
        db.execSQL(createTableQuery);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        // Handle the upgrade if needed
        String upgradeTableQuery = "DROP TABLE IF EXISTS mytable";
        db.execSQL(upgradeTableQuery);
        onCreate(db);
    }
}
```
## build.gradle
```
implementation 'androidx.sqlite:sqlite:2.2.0'
```
