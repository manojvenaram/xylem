# xylem
## MainActivity.java
```
package com.example.sqlitedatebase;

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
    private EditText idEditText, nameEditText, passwordEditText;
    private Button submitButton;
    private TextView displayTextView;
    private MyDatabaseHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        idEditText = findViewById(R.id.idEditText);
        nameEditText = findViewById(R.id.nameEditText);
        passwordEditText = findViewById(R.id.passwordEditText);
        submitButton = findViewById(R.id.submitButton);
        displayTextView = findViewById(R.id.displayTextView);

        dbHelper = new MyDatabaseHelper(this);
        SQLiteDatabase db = dbHelper.getWritableDatabase();

        submitButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String id = idEditText.getText().toString();
                String name = nameEditText.getText().toString();
                String password = passwordEditText.getText().toString();

                // Insert user input into the student table
                ContentValues values = new ContentValues();
                values.put("id", id);
                values.put("name", name);
                values.put("password", password);
                long newRowId = db.insert("student", null, values);

                // Display the data from the table
                String selectQuery = "SELECT * FROM student";
                Cursor cursor = db.rawQuery(selectQuery, null);

                StringBuilder stringBuilder = new StringBuilder();
                while (cursor.moveToNext()) {
                    String studentId = cursor.getString(cursor.getColumnIndex("id"));
                    String studentName = cursor.getString(cursor.getColumnIndex("name"));
                    String studentPassword = cursor.getString(cursor.getColumnIndex("password"));
                    stringBuilder.append("ID: ").append(studentId).append(", Name: ").append(studentName).append(", Password: ").append(studentPassword).append("\n");
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
        android:id="@+id/idEditText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter ID"
        android:inputType="number" />

    <EditText
        android:id="@+id/nameEditText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter Name"
        android:inputType="text" />

    <EditText
        android:id="@+id/passwordEditText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter Password"
        android:inputType="textPassword" />

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
import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

public class MyDatabaseHelper extends SQLiteOpenHelper {
    private static final String DATABASE_NAME = "student.db";
    private static final int DATABASE_VERSION = 1;

    public MyDatabaseHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        // Create the student table
        String createTableQuery = "CREATE TABLE student (id INTEGER PRIMARY KEY, name TEXT, password TEXT)";
        db.execSQL(createTableQuery);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        // Handle the upgrade if needed
        String upgradeTableQuery = "DROP TABLE IF EXISTS student";
        db.execSQL(upgradeTableQuery);
        onCreate(db);
    }
}

```
## build.gradle
```
implementation 'androidx.sqlite:sqlite:2.2.0'
```
