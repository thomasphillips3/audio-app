To implement pitch control in an Android native app using Kotlin, we'll use a third-party library called SoundTouch. SoundTouch is an open-source audio processing library that provides real-time pitch shifting. First, we'll use a wrapper library called TarsosDSP to make it easier to work with SoundTouch in an Android app.

Add the TarsosDSP library to your build.gradle (app module) file:
groovy

dependencies {
    // ... other dependencies ...
    implementation 'com.github.JorenSix:TarsosDSP:2.4'
}
Add a SeekBar for pitch control to your activity_main.xml layout file:
xml

<SeekBar
    android:id="@+id/pitchSeekBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:max="2000"
    android:progress="1000" />
Add the necessary imports to your MainActivity.kt file:
kotlin

import be.tarsos.dsp.AudioDispatcher
import be.tarsos.dsp.AudioEvent
import be.tarsos.dsp.AudioProcessor
import be.tarsos.dsp.io.android.AndroidFFMPEGLocator
import be.tarsos.dsp.io.android.AudioDispatcherFactory
import be.tarsos.dsp.pitch.PitchShifter
Declare the required variables in your MainActivity class:
kotlin

private var audioDispatcher: AudioDispatcher? = null
private var pitchShifter: PitchShifter? = null
private var audioProcessor: AudioProcessor? = null
Modify the playAudio function to use TarsosDSP with SoundTouch:
kotlin

private fun playAudio(audioFileUri: Uri) {
    val audioPath = getPathFromUri(audioFileUri)
    audioPath?.let { path ->
        stop() // Stop any currently playing audio
        AudioDispatcherFactory.setExtractor(AndroidFFMPEGLocator(this))
        audioDispatcher = AudioDispatcherFactory.fromPipe(path, 44100, 1024, 0)
        pitchShifter = PitchShifter(1.0, 44100, 1024, 10)
        audioProcessor = object : AudioProcessor {
            override fun processingFinished() {}

            override fun process(audioEvent: AudioEvent): Boolean {
                return pitchShifter?.process(audioEvent) ?: false
            }
        }
        audioProcessor?.let { processor ->
            audioDispatcher?.addAudioProcessor(processor)
            Thread(audioDispatcher).start()
        }
    }
}
Add a helper function to get the file path from the Uri:
kotlin

import android.provider.MediaStore

// ...
// Other imports
// ...

private fun getPathFromUri(uri: Uri): String? {
    val projection = arrayOf(MediaStore.Images.Media.DATA)
    val cursor = contentResolver.query(uri, projection, null, null, null)
    return if (cursor != null) {
        val columnIndex = cursor.getColumnIndexOrThrow(MediaStore.Images.Media.DATA)
        cursor.moveToFirst()
        val filePath = cursor.getString(columnIndex)
        cursor.close()
        filePath
    } else {
        uri.path
    }
}
In the onCreate function, set up a change listener for the pitch SeekBar:
kotlin

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    val pitchSeekBar: SeekBar = findViewById(R.id.pitchSeekBar)

    pitchSeekBar.setOnSeekBarChangeListener(object : SeekBar.OnSeekBarChangeListener {
        override fun onProgressChanged(seekBar: SeekBar?, progress: Int, fromUser: Boolean) {
            val pitchFactor = progress / 1000



Steven Grier
continue 
Continuing from the previous step, we'll set the pitch factor when the SeekBar progress changes.

Update the pitch factor inside the onProgressChanged callback:
kotlin

override fun onProgressChanged(seekBar: SeekBar?, progress: Int, fromUser: Boolean) {
    val pitchFactor = progress / 1000.0
    pitchShifter?.setPitchShift(pitchFactor)
}
Implement the remaining callbacks for the SeekBar change listener:
kotlin

override fun onStartTrackingTouch(seekBar: SeekBar?) {
    // Do nothing
}

override fun onStopTrackingTouch(seekBar: SeekBar?) {
    // Do nothing
}
Finally, add a stop() function to stop any currently playing audio when the pitch is changed or when a new audio file is played:
kotlin

private fun stop() {
    audioDispatcher?.stop()
    audioDispatcher?.removeAudioProcessor(audioProcessor)
}
Now, your Android native app allows users to change the pitch of the audio file in real-time. When users move the SeekBar, the pitch factor is adjusted accordingly, and the PitchShifter processes the audio in real-time to update the pitch.