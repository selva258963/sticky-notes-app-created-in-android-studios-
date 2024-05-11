# sticky-notes-app-created-in-android-studios-
## Aim:
The aim of adding a sticky note feature to GitHub is to provide users with a way to attach virtual sticky notes to specific files or lines of code within a repository. These notes can serve as reminders, comments, or annotations for collaborators working on the project.

## Algorithm:
1.Identification: Determine the files or lines of code to which the sticky notes can be attached.

2.User Interface: Design a user-friendly interface for creating, editing, and deleting sticky notes.

3.Storage: Implement a mechanism to store the sticky note data. This could be achieved by using GitHub's issue tracker, repository metadata, or a separate database.

4.Integration: Integrate the sticky note feature seamlessly into the GitHub interface, allowing users to access it directly from the repository page.

5.Permissions: Implement access control mechanisms to ensure that only authorized users can create or modify sticky notes.

6.Notifications: Optionally, provide notification mechanisms to alert collaborators when a sticky note is added or modified.
## program:
# Main java
```
package com.example.notesapp;
import android.appwidget.AppWidgetManager;
import android.content.ComponentName;
import android.widget.EditText;
import android.widget.RemoteViews;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    EditText mEditText;

    StickyNote note = new StickyNote();

    @Override
    protected void onCreate(android.os.Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mEditText = findViewById(R.id.editText);
        mEditText.setText(note.getStick(this));
    }

    public void updateWidget() {
        AppWidgetManager appWidgetManager = AppWidgetManager.getInstance(this);
        RemoteViews remoteViews = new RemoteViews(this.getPackageName(), R.layout.new_app_widget);
        ComponentName thisWidget = new ComponentName(this, AppWidget.class);

        remoteViews.setTextViewText(R.id.appwidget_text, mEditText.getText().toString());
        appWidgetManager.updateAppWidget(thisWidget, remoteViews);
    }

    public void saveButton(android.view.View v) {
        note.setStick(mEditText.getText().toString(), this);
        updateWidget();
        Toast.makeText(this, "Updated Successfully!!", Toast.LENGTH_SHORT).show();
    }
}

```
## App widget.java:
```
package com.example.notesapp;
import android.app.PendingIntent;
import android.appwidget.AppWidgetManager;
import android.appwidget.AppWidgetProvider;
import android.content.Context;
import android.content.Intent;
import android.widget.RemoteViews;

import com.example.notesapp.MainActivity;

public class AppWidget extends AppWidgetProvider {

    @Override
    public void onUpdate(Context context, AppWidgetManager appWidgetManager, int[] appWidgetIds) {
        for (int appWidgetId : appWidgetIds) {
            Intent launchActivity = new Intent(context, MainActivity.class);
            PendingIntent pendingIntent = PendingIntent.getActivity(context, 0, launchActivity, 0);

            RemoteViews remoteViews = new RemoteViews(context.getPackageName(), R.layout.new_app_widget);
            remoteViews.setOnClickPendingIntent(R.id.appwidget_text, pendingIntent);

            appWidgetManager.updateAppWidget(appWidgetId, remoteViews);
        }
    }
}
```
## StickyNotes.java:
```
package com.example.notesapp;
import android.content.Context;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileOutputStream;
import java.io.FileReader;
import java.io.IOException;

import static android.content.Context.MODE_PRIVATE;

class StickyNote {

    String getStick(Context context) {
        File fileEvents = new File(context.getFilesDir().getPath() + "/gfg.txt");
        StringBuilder text = new StringBuilder();

        try {
            BufferedReader br = new BufferedReader(new FileReader(fileEvents));
            String line;
            while ((line = br.readLine()) != null) {
                text.append(line);
                text.append('\n');
            }
            br.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

        return text.toString();
    }

    void setStick(String textToBeSaved, Context context) {
        String text = textToBeSaved;
        FileOutputStream fos = null;
        try {
            fos = context.getApplicationContext().openFileOutput("gfg.txt", MODE_PRIVATE);
            fos.write(text.getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fos != null) {
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```
## new_app_widgets_info.xml:
```
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/widgetLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#33B1EC"
    android:orientation="horizontal"
    android:padding="8dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:id="@+id/appwidget_text"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Text"
            android:textColor="#2C2A2A"
            android:textSize="20sp"

            tools:layout_editor_absoluteX="1dp"
            tools:layout_editor_absoluteY="1dp" />

    </LinearLayout>

</FrameLayout>

```
## app_widgets_info.xml:
```
<?xml version="1.0" encoding="utf-8"?>
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/app_widget_description"
    android:initialKeyguardLayout="@layout/app_widget"
    android:initialLayout="@layout/app_widget"
    android:minWidth="40dp"
    android:minHeight="40dp"
    android:previewImage="@drawable/example_appwidget_preview"
    android:previewLayout="@layout/app_widget"
    android:resizeMode="horizontal|vertical"
    android:targetCellWidth="1"
    android:targetCellHeight="1"
    android:updatePeriodMillis="86400000"
    android:widgetCategory="home_screen" />
```
## app_widgets.xml:
```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    style="@style/Widget.Notesapp.AppWidget.Container"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:theme="@style/Theme.Notesapp.AppWidgetContainer">

    <TextView
        android:id="@+id/appwidget_text"
        style="@style/Widget.Notesapp.AppWidget.InnerView"
        android:layout_width="181dp"
        android:layout_height="196dp"
        android:layout_centerInParent="true"
        android:layout_marginStart="8dp"
        android:layout_marginLeft="8dp"
        android:layout_marginTop="8dp"
        android:layout_marginEnd="8dp"
        android:layout_marginRight="8dp"
        android:layout_marginBottom="8dp"
        android:contentDescription="@string/appwidget_text"
        android:text="@string/appwidget_text"
        android:textColor="@color/black"
        android:textSize="24sp"
        android:textStyle="bold|italic" />
</RelativeLayout>
```
## activity_main.xml:
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/editText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter your note here"
        android:textSize="16sp"
        android:padding="8dp"
        android:gravity="top"
        android:minLines="5"
        android:maxLines="10"
        android:scrollbars="vertical"/>

    <Button
        android:id="@+id/saveButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/editText"
        android:layout_alignParentEnd="true"
        android:layout_marginTop="16dp"
        android:text="Save"
        android:textColor="@color/black"
        android:onClick="saveButton"/>

</RelativeLayout>

```
## output:
![Screenshot (84)](https://github.com/selva258963/sticky-notes-app-created-in-android-studios-/assets/121961701/f9950b19-0c1d-41e7-b5a1-e6fb4d64b70d)
![Screenshot (83)](https://github.com/selva258963/sticky-notes-app-created-in-android-studios-/assets/121961701/38f8bff7-dbc4-4c94-93af-096490ff5ba7)
![Screenshot (80)](https://github.com/selva258963/sticky-notes-app-created-in-android-studios-/assets/121961701/439f634b-c3c4-49ff-b404-45bb3cdac05f)
![Screenshot (79)](https://github.com/selva258963/sticky-notes-app-created-in-android-studios-/assets/121961701/f1064f22-3ded-4c36-9fbb-bdf3db4a7532)
![Screenshot (82)](https://github.com/selva258963/sticky-notes-app-created-in-android-studios-/assets/121961701/b4a0f9bc-f450-43d8-aa4f-cdaa89d1d89a)
![Screenshot (78)](https://github.com/selva258963/sticky-notes-app-created-in-android-studios-/assets/121961701/c37e25c6-7290-4b6d-8cd9-eea542ba6449)
![Screenshot (77)](https://github.com/selva258963/sticky-notes-app-created-in-android-studios-/assets/121961701/8c41ad03-62ff-46f9-9027-f532f7818c1e)
![Screenshot (81)](https://github.com/selva258963/sticky-notes-app-created-in-android-studios-/assets/121961701/d9ea33c9-e361-4e9e-94b8-cef286de8712)
![Screenshot (76)](https://github.com/selva258963/sticky-notes-app-created-in-android-studios-/assets/121961701/02e5b5d0-5d8e-44b9-9077-11628e54ede6)

## Result:
Upon successful implementation, users will be able to:

Add sticky notes to specific files or lines of code within a GitHub repository.
View existing sticky notes and edit or delete them as needed.
Collaborate with team members by sharing important information or reminders using sticky notes.
Enhance communication and organization within the repository by providing a visual representation of annotations and comments directly within the codebase.
