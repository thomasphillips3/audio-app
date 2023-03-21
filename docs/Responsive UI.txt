To implement a responsive UI in an Android native app using Kotlin, follow these steps:

Use ConstraintLayout as the root layout for your activities. ConstraintLayout allows you to create flexible and responsive UIs by defining constraints between UI elements. Replace the root layout in your activity_main.xml with a ConstraintLayout:
xml

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <!-- Add UI elements here -->

</androidx.constraintlayout.widget.ConstraintLayout>
Define constraints between UI elements. For example, add a Button and a TextView in the activity_main.xml and set constraints to make them responsive:
xml

<Button
    android:id="@+id/button"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Button"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

<TextView
    android:id="@+id/textView"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Responsive UI Example"
    android:textSize="24sp"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />
Use dimensions resources to provide different values for different screen sizes or orientations. In the res directory, create a new directory called values-sw600dp for devices with a minimum width of 600dp. Inside this directory, create a new file called dimens.xml:
xml

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <dimen name="text_size_large">32sp</dimen>
</resources>
In the default values directory, create a dimens.xml file if it doesn't exist already, and add the following dimension:
xml

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <dimen name="text_size_large">24sp</dimen>
</resources>
Replace the hardcoded textSize value in your TextView with the dimension resource:
xml

<TextView
    ...
    android:textSize="@dimen/text_size_large" />
Now, your Android native app has a responsive UI that adapts well to different screen sizes and orientations. The ConstraintLayout helps you define constraints between UI elements for flexible positioning, and dimension resources allow you to provide different values for different screen sizes. Keep refining the constraints and dimensions as needed to achieve the desired responsive behavior for your app.